# Review an Ansible community package collection inclusion request

This document outlines the process for an AI agent to review an Ansible collection for inclusion in the Ansible community package.

## Prerequisites

- The agent must be provided with the local path to the collection's source code.

## Workflow

1.  **Parse Collection Metadata**:
    -   Locate and parse the `galaxy.yml` file in the collection's root directory.
    -   Extract key metadata: `namespace`, `name`, `version`, `repository`, `authors`, `license`, and `tags`.

2.  **Conduct Checklist-Based Review**:
    -   Systematically go through each item in `collection_checklist.md`.
    -   For each item, follow the detailed verification steps and refer to the documentation outlined in the "Sources and Verification Steps" section below.
    -   Keep a record of the findings for each checklist item (pass, fail, or needs manual review).

3.  **Generate Report**:
    -   Based on the completed checklist, generate a report summarizing the findings.
    -   For any failed checks, provide a clear explanation and reference the relevant requirement.
    -   Highlight areas that may require manual human review.

## Sources and Verification Steps

This section provides file paths to the relevant documentation and standards to be used during the review.

-   **Collection Requirements**: `ansible-documentation/docs/docsite/rst/community/collection_contributors/collection_requirements.rst`
-   **Collection Checklist**: `collection_checklist.md`
-   **Module Format and Documentation Guide**: `ansible-documentation/docs/docsite/rst/dev_guide/developing_modules_documenting.rst`

### Verification Guidance for `collection_checklist.md` items:

*   **"published on Ansible Galaxy..."**:
    *   Construct the Galaxy URL: `https://galaxy.ansible.com/{namespace}/{name}` using the values from `galaxy.yml`.
    *   Verify that the URL is reachable and the collection is present.

*   **"have a public git repository"**:
    *   Use the `repository` URL from `galaxy.yml`.
    *   Verify the URL is accessible.

*   **"has `README.md`"**:
    *   Check for the existence of a `README.md` file in the root of the collection directory.

*   **"collection repository should not contain any unnecessary files..."**:
    *   Check for the existence of a `.gitignore` file in the root of the collection directory.
    *   The `.gitignore` file should contain entries for common temporary files and directories.

*   **"documentation and return sections use `version_added:`..."**:
    *   For each module, parse the `DOCUMENTATION` string.
    *   Check that `version_added` is present for the module/plugin itself and for its options. The version should be the collection version, not `ansible-core` version.

*   **"follows the Ansible documentation standards..."**:
    *   Review module and plugin documentation against the guidelines in `ansible-documentation/docs/docsite/rst/dev_guide/developing_modules_documenting.rst`.
    *   Check for adherence to best practices mentioned in `ansible-documentation/docs/docsite/rst/dev_guide/developing_modules_best_practices.rst`.
    *   The `check_mode` support is specified in the `DOCUMENTATION` block of modules files.

*   **"supports all Python versions..."**:
    *   Check `meta/runtime.yml` for the minimum `ansible-core` version.
    *   Cross-reference this with the Python support matrix in `ansible-documentation/docs/docsite/rst/reference_appendices/release_and_maintenance.rst`.
    *   If there are exceptions, verify they are documented in the collection's `README.md` and documentation fragments/requirements module documentation sections.

*   **"follows development conventions..."**:
    *   **Idempotency**: Review module logic to ensure that running it multiple times with the same parameters results in the same state. This often requires manual inspection.
    *   **`_info` modules**: Check that modules ending in `_info.py` only gather information and do not make any changes to the system. Their names should correspond to the information they gather (e.g., `user_info`).
    *   **`_facts` modules**: Verify that modules ending in `_facts.py` return `ansible_facts` and do not return other data.
    *   **No query state in modules**: Other modules should not have options like `state=get` or `state=query`. Such functionality should be in separate `_info` or `_facts` modules.
    *   **`check_mode` support**: Ensure that all `_info` and `_facts` modules support `check_mode`. Look for `supports_check_mode=True` in the `AnsibleModule` argument spec.
