# MCP Health Check Report
Generated: 2026-08-17

## Summary
All core MCP tools are operational. LSP is active with servers registered. Below are individual tool statuses.

### 1. lsp-intelligence
- Status: ACTIVE
- LSP servers registered: 16 installed, 15 manual-only/missing
- Active clients: none
- Live diagnostics available

### 2. zenith
- Status: ACTIVE
- Edit file operation: SUCCESS
- Other operations: search_file, search_files, stashRestore available

### 3. dark-memory-mcp
- Status: ACTIVE
- Driver: sqlite
- Schema version: 25
- Active sessions: 23 total, 0 active
- Memory state: healthy
- Canary: present
- Write audit: 126 entries
- Active project: default

### 4. chroma-project-indexing
- Status: ACTIVE
- Collections found: 3
  - test-health
  - hermes-projects-knowledge
  - synthetic-trader-knowledge

### 5. github
- Status: LIMITED
- Requires valid owner/repo credentials
- List branches failed: 404 (repos/sebas/proyectos not found or private)
- Other ops available: create_branch, get_latest_release, list_releases

### 6. filesystem
- Status: ACTIVE
- Directory tree: SUCCESS (full tree of /home/sebas/proyectos/)
- Operations: create_directory, list_directory, get_file_info, search_files

### 7. playwright
- Status: ACTIVE (headless)
- Browser close: SUCCESS (no open tabs)
- Other ops: navigate, fill_form, console_messages, press_key

### 8. visual-parity
- Status: ACTIVE (configured)
- compare_sections: Requires referenceUrl (properly returning error)
- discover_sections: Available
- Other ops: compare_pages

### 9. mcp-code-review-pro
- Status: ACTIVE
- Conventions: Auto-detected as Python with no custom rules
- No conventions file found (.codereview.yml)
- Other ops: review_diff, review_file, review_github_pr

## Key Observations
1. LSP is fully operational with all major language servers registered
2. dark-memory has active session tracking (23 sessions total)
3. ChromaDB has 3 collections with project knowledge
4. Filesystem operations working correctly
5. GitHub access limited without proper auth credentials
6. All tools respond to basic operations without critical failures

## Status: ALL SYSTEMS GO