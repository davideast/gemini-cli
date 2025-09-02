# Implementing `--with-thoughts` for Debugging

This document outlines the plan to implement a new feature that allows developers to include the model's "thoughts" in saved chat histories. This will be invaluable for debugging and understanding the model's reasoning process.

## Goal

The primary goal is to add a `--with-thoughts` flag to the `/chat save` command. When this flag is used, the saved chat history will include the `thought` parts of the model's output, which are currently filtered out by default.

## Key Files

The following files have been identified as relevant to this change:

-   **`packages/cli/src/ui/commands/chatCommand.ts`**: This file contains the definition of the `/chat save` command. It will be modified to add the new `--with-thoughts` flag and to pass that option along to the history retrieval logic.
-   **`packages/core/src/core/client.ts`**: This file contains the `getHistory` method, which is responsible for retrieving the chat history. It has a `stripThoughts` parameter that will need to be controlled by the new flag.
-   **`packages/core/src/core/geminiChat.ts`**: This file contains the core chat logic and is where the `thought` parts are filtered from the history.
-   **`packages/core/src/services/chatRecordingService.ts`**: This file contains the `saveCheckpoint` method, which is responsible for writing the chat history to a file.

## Implementation Plan

The implementation will be broken down into the following steps:

1.  **Modify the `saveCommand` in `chatCommand.ts`**:
    -   Update the `description` of the `saveCommand` to include the new `--with-thoughts` flag.
    -   In the `action` handler, parse the command arguments to detect the presence of the `--with-thoughts` flag.

2.  **Update the `getHistory` call**:
    -   In the `action` handler for the `saveCommand`, when calling `chat.getHistory()`, pass `{ stripThoughts: false }` if the `--with-thoughts` flag is present.

3.  **Verify the `saveCheckpoint` method**:
    -   Ensure that the `saveCheckpoint` method in `chatRecordingService.ts` correctly serializes the chat history, including the `thought` parts, when they are present. No changes are anticipated here, but it will be verified during testing.

## Example Usage

Once implemented, the new flag will be used as follows:

```
/chat save my-debug-session --with-thoughts
```
