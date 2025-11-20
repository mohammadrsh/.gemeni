# Gemini CLI Helper Configuration

This repository contains configurations, plans, and custom commands for the Gemini CLI helper. The `.gemini` directory is crucial for extending the functionality and tailoring the Gemini CLI to your specific needs and workflows.

## Importing the `.gemini` folder from a GitHub Repository (One-time Pull)

If you have a `.gemini` configuration stored in a separate GitHub repository and wish to include it as a subfolder in your current project, you can perform a one-time pull/copy operation. This method is suitable if you don't need to track upstream changes to the `.gemini` folder within your project using `git submodule`.

### Steps to import:

1.  **Navigate to your project's root directory**:

    ```bash
    cd /path/to/your/project
    ```

2.  **Clone the `.gemini` repository**:
    ```bash
    git clone https://github.com/mohammadrsh/.gemeni
    ```

This setup ensures that your Gemini CLI configurations are neatly organized and integrated into your project. If you need to update these configurations later, you will need to repeat the cloning and copying process, or manually merge changes.
