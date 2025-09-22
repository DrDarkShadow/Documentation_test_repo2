## Overview

This file provides the `RepoAnalyzer` class, a tool designed to analyze a local Git repository. Its primary function is to detect changes in code at a granular level, specifically identifying added, removed, and modified functions and classes within tracked files. It can analyze either staged changes or all uncommitted changes (staged and unstaged). The analysis respects ignore patterns and file extension filters defined in the application's configuration.

## Classes

### RepoAnalyzer

Analyzes a Git repository for code changes at the object level (functions and classes).

The analyzer interfaces with a Git repository to compare the state of files between the `HEAD` commit and the current working state (either the index for staged changes or the working directory for all changes).

#### `__init__(repo_path: str)`

Initializes the `RepoAnalyzer` instance.

It sets up the connection to the Git repository at the specified path. If the path is not a valid Git repository, it prints an error and raises a `git.InvalidGitRepositoryError`.

*   **Parameters**:
    *   `repo_path` (str): The file system path to the Git repository.

#### `_is_ignored(file_path: str) -> bool`

Checks if a given file path should be ignored based on the configured patterns.

This is a helper method that iterates through the `CONFIG.ignore_patterns` list and uses `fnmatch` to check if the file path matches any of the patterns.

*   **Parameters**:
    *   `file_path` (str): The path of the file to check.
*   **Returns**:
    *   `bool`: `True` if the file path matches an ignore pattern, `False` otherwise.

#### `calculate_fibonacci(n)`

Calculates the n-th Fibonacci number using an iterative approach.

This function is designed to be a clear example of a new, distinct piece of logic added to test the documentation agent's ability to intelligently place new documentation snippets.

*   **Parameters**:
    *   `n` (int): The position in the Fibonacci sequence.
*   **Returns**:
    *   `int`: The n-th Fibonacci number.

#### `_get_file_content(commit_hash: str, file_path: str) -> str`

Retrieves the content of a file from a specific commit.

This helper method accesses the Git repository's object database to read the content of a file as it existed in a particular commit.

*   **Parameters**:
    *   `commit_hash` (str): The hash of the commit to retrieve the file from. Can also be a reference like "HEAD".
    *   `file_path` (str): The path to the file within the repository.
*   **Returns**:
    *   `str`: The decoded content of the file. Returns an empty string if the file did not exist in that commit or if an error occurred.

#### `analyze_changes(staged_only: bool = True) -> Dict[str, Dict[str, Any]]`

Analyzes file changes to identify added, removed, and modified code objects (functions and classes).

This is the main method of the class. It diffs the repository to find changed files. For each changed file, it parses the old and new versions to extract function and class definitions, then compares these sets to determine what was added, removed, or modified.

*   **Parameters**:
    *   `staged_only` (bool, optional): If `True` (default), analyzes only staged changes (HEAD vs. index). If `False`, analyzes all uncommitted changes, including staged, unstaged, and untracked files.
*   **Returns**:
    *   `Dict[str, Dict[str, Any]]`: A dictionary where keys are the file paths of changed files. Each value is another dictionary containing:
        *   `"status"` (str): The change type ('A' for added, 'M' for modified, 'D' for deleted, etc.).
        *   `"added"` (List[Dict]): A list of code objects that were added.
        *   `"removed"` (List[Dict]): A list of code objects that were removed.
        *   `"modified"` (List[Dict]): A list of code objects that were modified.

##### Usage Example

```python
from code_monitor.analyzer import RepoAnalyzer

try:
    # Initialize the analyzer with the path to your repo
    analyzer = RepoAnalyzer('/path/to/your/git/repo')

    # Analyze only the staged changes
    staged_changes = analyzer.analyze_changes(staged_only=True)

    print("--- Staged Changes ---")
    for file, changes in staged_changes.items():
        print(f"File: {file} (Status: {changes['status']})")
        if changes['added']:
            print("  Added:")
            for item in changes['added']:
                print(f"    - {item['type']} {item['name']}")
        if changes['modified']:
            print("  Modified:")
            for item in changes['modified']:
                print(f"    - {item['type']} {item['name']}")
        if changes['removed']:
            print("  Removed:")
            for item in changes['removed']:
                print(f"    - {item['type']} {item['name']}")

except git.InvalidGitRepositoryError:
    print("Failed to initialize analyzer.")
```


<!-- DOC_START: code_monitor/analyzer.py::RepoAnalyzer -->
This function analyzes a Git repository to identify code changes at the "object" level, such as functions, classes, or methods. It operates by comparing the Abstract Syntax Trees (ASTs) of files between different commits. This provides a more granular view of changes than a standard file-level diff, making it ideal for targeted code reviews, impact analysis, or automated changelog generation.

The analysis process involves:
1.  Initializing a Git repository object from the provided path.
2.  Iterating through the commits specified in the `commit_range`.
3.  For each commit, identifying modified Python files.
4.  For each modified file, fetching the content of the file before and after the change.
5.  Parsing both versions into code object trees using a language-specific parser.
6.  Comparing the trees to pinpoint added, removed, or modified objects.
7.  Aggregating these granular changes into a structured list for the entire commit range.

### Parameters

| Name           | Type | Description                                                                                                                              |
| :------------- | :--- | :--------------------------------------------------------------------------------------------------------------------------------------- |
| `repo_path`    | `str`  | The absolute or relative file system path to the local Git repository to be analyzed.                                                    |
| `commit_range` | `str`  | A string defining the range of commits to inspect, in a format accepted by `git rev-list` (e.g., `'HEAD~1..HEAD'`, `'main..develop'`). |

### Returns

| Type                  | Description                                                                                                                                                           |
| :-------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `List[Dict[str, any]]` | A list of dictionaries, where each dictionary represents a single detected change to a code object. An empty list is returned if no changes are found. Each dictionary contains the following keys:<br><br>**`file_path`** (`str`): The relative path to the modified file within the repository.<br>**`object_name`** (`str`): The fully qualified name of the changed object (e.g., `my_module.MyClass.my_method`).<br>**`change_type`** (`str`): The type of change. Can be `'added'`, `'modified'`, or `'deleted'`.<br>**`commit_hash`** (`str`): The full SHA of the commit where the change occurred. |

### Raises

| Type                          | Description                                                                 |
| :---------------------------- | :-------------------------------------------------------------------------- |
| `git.exc.InvalidGitRepositoryError` | Raised if the provided `repo_path` does not point to a valid Git repository. |
| `ValueError`                  | Raised if the `commit_range` is invalid or cannot be resolved by Git.       |
<!-- DOC_END: code_monitor/analyzer.py::RepoAnalyzer -->

