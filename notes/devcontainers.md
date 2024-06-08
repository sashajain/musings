Control versions of dev tools...


- ./.devcontainer/devcontainer.json
- ./.devcontainer/Dockerfile
- ./.devcontainer/dev-requirements.txt
- ./requirements.txt
- ./.sqlfluff


./.devcontainer/devcontainer.json:

```
{

  "build": {
    "dockerfile": "Dockerfile",
    "context": ".."
  }
  "customizations": {
    "vscode": {
      "extensions": [
        "ms-python.python",
        "ms-python.mypy-type-checker",
        "ms-python.black-formatter",
        "ms-python.pylint",
        "ms-python.isort",
        "dorzey.sqlfluff",
        ]
    }
  },
  "settings": {
    "mypy-type-checker.importStrategy": "fromEnvironment",
    "black-formatter.importStrategy": "fromEnvironment",
    "pylint.importStrategy": "fromEnvironment",
    "isort.importStrategy": "fromEnvironment"
  },

}
```