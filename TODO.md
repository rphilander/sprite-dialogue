# sprite-dialogue TODO

Small enhancements and known issues to address.

## Enhancements

- [x] Pass original filename through channel meta as `file_name`
- [ ] Auto-restart the bun server on file changes during development (consider `bun --hot server.ts` or watch flag)
- [ ] Add a connection status indicator that's more visible (the 8px dot is subtle)
- [x] Plugin path (`--plugin-dir`) confirmed working for channels — the fix was using the right dev-flag tag: `plugin:sprite-dialogue@inline` (not `server:sprite-dialogue`). The "inline" marketplace name is what `--plugin-dir` synthesizes.

## Known Issues

- [ ] Orphan bun processes survive when claude exits abruptly, blocking port reuse on restart (need a cleanup hook or a way for claude to kill its MCP children on exit)
- [x] Occasional message drops: fixed by adding a client-side outbound queue that buffers messages when the WebSocket is reconnecting. Server-side logging at `/tmp/sprite-dialogue.log` left in place to catch any recurrence.

## Resolved

- [x] Stop-hook race: assistant text often missing from UI echoes because the hook fired before Claude flushed the final text block to the JSONL. Replaced both Stop and UserPromptSubmit hooks with a polling transcript watcher inside the bun MCP server (`~/.claude/projects/*/*.jsonl`, 300ms tick, per-file byte offsets, partial-line buffering). Race-free by construction.

## Testing checklist

- [x] Text message: user → Claude
- [x] Text message: Claude → user (via transcript watcher)
- [x] Image: clipboard paste (Cmd+V)
- [x] Image: drag-and-drop
- [x] Image: Attach button (file picker)
- [x] Auto-scroll on new message (fixed)
- [ ] Lightbox zoom on click
- [ ] Image: Claude → user (via `send_image` tool)
- [ ] Multi-image upload
- [ ] Multi-tab synchronization (open dialogue in two tabs, verify both update)
- [ ] Multi-block turns (text + tool + text): both text segments mirror to UI
