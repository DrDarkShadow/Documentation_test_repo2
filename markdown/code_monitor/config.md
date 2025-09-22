## Overview

This module handles the configuration management for the code monitoring tool. It defines a `MonitorConfig` class that loads settings from a YAML file (`.monitor_config.yaml` by default). These settings determine which file extensions to monitor and which file or directory patterns to ignore. A global singleton instance, `CONFIG`, is created and exported for easy access to these settings throughout the application.

## Classes

### MonitorConfig

A class to load and store configuration settings from a YAML file.

The `MonitorConfig` class is responsible for encapsulating the application's configuration. When an object of this class is created, it initializes with default settings and then attempts to override them by reading a specified YAML configuration file. If the configuration file does not exist, the default values are used.

The two main configuration parameters are:
- `ignore_patterns`: A list of glob patterns for files and directories to be ignored by the monitor.
- `file_extensions`: A list of file extensions (e.g., `.py`, `.md`) that the monitor should track.

#### `__init__(config_path='.monitor_config.yaml')`

Initializes the `MonitorConfig` instance.

This constructor sets up the initial state with default values. It then checks if the file at `config_path` exists. If it does, the method opens and parses the YAML file, updating the instance's attributes with the values found in the file. Keys not present in the YAML file will retain their default values.

*   **Parameters:**
    *   `config_path` (str, optional): The path to the YAML configuration file. Defaults to `'.monitor_config.yaml'`.

*   **Returns:**
    *   `None`

*   **Usage Example:**

An example of a `.monitor_config.yaml` file:

```yaml
# .monitor_config.yaml
# List of glob patterns to ignore
ignore_patterns:
  - "*/.venv/*"
  - "*/__pycache__/*"
  - "*.tmp"
  - "docs/_build/*"

# List of file extensions to monitor
file_extensions_to_check:
  - ".py"
  - ".md"
  - ".yaml"
```

When `MonitorConfig` is instantiated, it will load these values.

```python
# Assuming the .monitor_config.yaml above exists
config = MonitorConfig()

print(config.ignore_patterns)
# Output: ['*/.venv/*', '*/__pycache__/*', '*.tmp', 'docs/_build/*']

print(config.file_extensions)
# Output: ['.py', '.md', '.yaml']
```

## Global Instances

### `CONFIG`

A global, pre-initialized instance of the `MonitorConfig` class.

*   **Type:** `MonitorConfig`

*   **Description:**
    This singleton instance is created when the `config.py` module is first imported. It automatically attempts to load configuration from `.monitor_config.yaml` in the current working directory. It is the intended way for other parts of the application to access configuration settings.

*   **Usage Example:**

```python
from code_monitor.config import CONFIG

# Use the global CONFIG instance to access settings
def should_ignore_file(filepath):
    # Access ignore_patterns directly from the global config
    for pattern in CONFIG.ignore_patterns:
        if fnmatch.fnmatch(filepath, pattern):
            return True
    return False

def is_valid_extension(filepath):
    # Access file_extensions directly from the global config
    return any(filepath.endswith(ext) for ext in CONFIG.file_extensions)
```


<!-- DOC_START: code_monitor/config.py::MonitorConfig -->
yaml
github:
  token: "your_github_personal_access_token"
  owner: "github_username_or_org"
  repo: "repository_name"

monitor:
  check_interval: 60
  commit_check_depth: 5
  target_branch: "main"

actions:
  run_command: "echo 'New commit found!'"
<!-- DOC_END: code_monitor/config.py::MonitorConfig -->



<!-- DOC_ID: code_monitor/config.py----sub_num -->
<!-- DOC_START: code_monitor/config.py::sub_num -->
python
result = sub_num(10, 3)
print(result)
# Expected output: 7
<!-- DOC_END: code_monitor/config.py::sub_num -->
<!-- END_DOC_ID: code_monitor/config.py----sub_num -->

