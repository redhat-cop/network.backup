# Parser-Based Feature Extraction

This directory contains parser definitions for extracting configuration change features from diff output. The parser-based approach makes it easy to customize and extend feature detection.

## Overview

Instead of hardcoding regex patterns in the main task file, parsers are defined as separate YAML files. Each parser:
- Defines patterns to detect specific configuration changes
- Has a configurable weight for severity scoring
- Can be easily added, modified, or disabled

## Parser Structure

Each parser file (`*_parser.yaml`) follows this structure:

```yaml
---
parser_name: parser_name          # Unique identifier
feature_name: feature_changes     # Key in diff_features dictionary
weight: 10                        # Points per change (for scoring)
description: "Human readable description"

patterns:
  add:                            # Patterns for added lines
    - '^\\+pattern1'
    - '^\\+pattern2'
  remove:                         # Patterns for removed lines
    - '^-pattern1'
    - '^-pattern2'

# Optional: Exclude patterns
exclude_patterns:
  - 'pattern_to_exclude'
```

## Built-in Parsers

### 1. `bgp_parser.yaml`
- **Feature**: `bgp_changes`
- **Weight**: 10 points
- **Detects**: BGP router configuration changes

### 2. `acl_parser.yaml`
- **Feature**: `acl_changes`
- **Weight**: 10 points
- **Detects**: Access Control List configuration changes

### 3. `security_parser.yaml`
- **Feature**: `security_changes`
- **Weight**: 10 points
- **Detects**: Security-related changes (AAA, TACACS, RADIUS, usernames, crypto keys)

### 4. `routing_parser.yaml`
- **Feature**: `routing_changes`
- **Weight**: 5 points
- **Detects**: Routing configuration changes (static routes, routing protocols)

### 5. `vlan_parser.yaml`
- **Feature**: `vlan_changes`
- **Weight**: 5 points
- **Detects**: VLAN configuration changes

### 6. `interface_parser.yaml`
- **Feature**: `interface_changes`
- **Weight**: 2 points
- **Detects**: Interface configuration changes (excluding descriptions)

### 7. `description_parser.yaml`
- **Feature**: `description_changes`
- **Weight**: 1 point
- **Detects**: Interface description changes (lowest risk)

## Creating Custom Parsers

### Step 1: Create Parser File

Create a new file in the `parsers/` directory following the naming convention: `{feature}_parser.yaml`

Example: `ospf_parser.yaml`

```yaml
---
parser_name: ospf_parser
feature_name: ospf_changes
weight: 8
description: "Detects OSPF routing protocol configuration changes"

patterns:
  add:
    - '^\\+router\\s+ospf'
    - '^\\+router\\s+ospfv3'
    - '^\\+ip\\s+ospf'
  remove:
    - '^-router\\s+ospf'
    - '^-router\\s+ospfv3'
    - '^-ip\\s+ospf'
```

### Step 2: Parser is Automatically Loaded

The `load_parsers.yaml` task automatically discovers and loads all `*_parser.yaml` files in this directory. No additional configuration needed!

### Step 3: Use in Scoring (Optional)

If you want to use the parser's weight dynamically in scoring, enable dynamic scoring:

```yaml
vars:
  use_dynamic_scoring: true
```

Otherwise, the weight is used for reference and you can manually add it to the scoring formula in `diff_severity_scoring.yaml`.

## Pattern Syntax

Patterns use Python regex syntax (as used by Ansible's `regex_findall` filter):

- `^` - Start of line
- `\\+` - Literal `+` (for added lines in diff)
- `\\-` - Literal `-` (for removed lines in diff)
- `\\s+` - One or more whitespace characters
- `\\d+` - One or more digits
- `|` - OR operator (when joining patterns)

### Common Patterns

```yaml
# Match interface configuration
'^\\+interface\\s+'

# Match with specific value
'^\\+vlan\\s+\\d+'

# Match multiple options
'^\\+(ip|ipv6)\\s+access-list'

# Match with word boundary
'^\\+router\\s+bgp\\b'
```

## Advanced: Exclude Patterns

Some parsers may need to exclude certain patterns. For example, interface changes shouldn't count descriptions:

```yaml
exclude_patterns:
  - 'description'
```

Note: Currently, exclusions are handled in the scoring formula. Future enhancements may add automatic exclusion logic.

## Testing Parsers

### Enable Debug Mode

To see which parsers are loaded and what features are detected:

```yaml
vars:
  debug_parsers: true
```

### Manual Testing

You can test parser patterns manually:

```bash
# Create a test diff
echo "+router bgp 65000" > test_diff.txt
echo "-interface GigabitEthernet1" >> test_diff.txt

# Test with ansible
ansible localhost -m debug -a "msg={{ lookup('file', 'test_diff.txt') | regex_findall('^\\+router\\s+bgp', multiline=True) }}"
```

## Disabling Parsers

To temporarily disable a parser, rename it (remove `_parser.yaml` suffix) or move it outside the `parsers/` directory.

Example:
```bash
mv bgp_parser.yaml bgp_parser.yaml.disabled
```

## Parser Priority

Parsers are loaded in alphabetical order. If multiple parsers match the same line, all matches are counted. This is usually desired behavior (e.g., a BGP change might also match a routing change).

## Best Practices

1. **Use Descriptive Names**: `ospf_parser.yaml` is better than `parser1.yaml`
2. **Document Patterns**: Add comments explaining complex patterns
3. **Test Thoroughly**: Test with real diff output from your devices
4. **Keep Patterns Specific**: Avoid overly broad patterns that match unintended changes
5. **Consider Weights**: Higher weights for more critical changes
6. **Version Control**: Track parser changes in git for auditability

## Example: Adding OSPF Parser

1. Create `parsers/ospf_parser.yaml`:
```yaml
---
parser_name: ospf_parser
feature_name: ospf_changes
weight: 8
description: "Detects OSPF routing protocol configuration changes"

patterns:
  add:
    - '^\\+router\\s+ospf'
    - '^\\+router\\s+ospfv3'
  remove:
    - '^-router\\s+ospf'
    - '^-router\\s+ospfv3'
```

2. Update scoring formula in `diff_severity_scoring.yaml` (if not using dynamic scoring):
```yaml
severity_score: >-
  {{
    (diff_features.bgp_changes | default(0) | int) * 10 +
    (diff_features.ospf_changes | default(0) | int) * 8 +  # New parser
    # ... rest of formula
  }}
```

3. That's it! The parser is automatically loaded and used.

## Troubleshooting

### Parser Not Detected
- Check file naming: Must end with `_parser.yaml`
- Check file location: Must be in `parsers/` directory
- Check YAML syntax: Use `ansible-lint` or `yamllint`

### Patterns Not Matching
- Test patterns manually with `regex_findall`
- Check for escaped characters (`\\+` not `+`)
- Verify multiline mode is enabled
- Check diff format (unified diff with `+`/`-` prefixes)

### Wrong Feature Counts
- Check for overlapping patterns between parsers
- Verify exclude patterns are working
- Enable `debug_parsers: true` to see execution summary

## Migration from Hardcoded Patterns

The old hardcoded approach:
```yaml
bgp_changes: "{{ (diff_output.stdout | regex_findall('^\\+router\\s+bgp', multiline=True) | length) + ... }}"
```

New parser-based approach:
```yaml
# Defined in bgp_parser.yaml
# Automatically loaded and executed
```

Benefits:
- ✅ Easier to maintain
- ✅ Easier to extend
- ✅ Better organization
- ✅ Reusable patterns
- ✅ Self-documenting
