# cc-model-detector

**Experimental script** that attempts to detect which Claude model (Opus/Sonnet/Haiku) is currently active in Claude Code.

## ⚠️ Important Disclaimer

This is an **unofficial workaround**. Since Anthropic doesn't provide an official way to detect the active model, this script reads from local JSONL log files and shows the most recently used model. This means:

- It may lag behind actual model switches
- It shows the last logged model, not necessarily the current one
- It could break if Claude Code changes its logging format

## Installation

```bash
# Download the script
curl -o ~/.local/bin/cc-model-detector https://raw.githubusercontent.com/daveremy/cc-model-detector/main/cc-model-detector
chmod +x ~/.local/bin/cc-model-detector
```

## Usage

### Basic Usage
```bash
# Show formatted output (with emoji)
cc-model-detector
# Output: 🎭 Opus

# Show raw model name
cc-model-detector raw
# Output: claude-opus-4-20250514

# Watch for changes
cc-model-detector watch
# Output: [12:34:56] Model: claude-opus-4-20250514
```

### Use with tmux Status Bar
Add to your `~/.tmux.conf`:
```bash
set -g status-right '#[fg=cyan]#(~/.local/bin/cc-model-detector) #[fg=yellow]| %H:%M %d-%b-%y'
set -g status-interval 5
```

### Use with macOS `watch` Command
```bash
# Install watch if needed
brew install watch

# Monitor model changes
watch -n 2 cc-model-detector
```

### Use in Scripts
```bash
#!/bin/bash
MODEL=$(cc-model-detector raw)
if [[ $MODEL == *"opus"* ]]; then
    echo "Running on Opus - go ahead with complex task"
else
    echo "Running on $MODEL - might want to wait for Opus"
fi
```

### Use with Terminal Notifier
```bash
# Get notified when model changes
LAST_MODEL=""
while true; do
    CURRENT=$(cc-model-detector raw)
    if [ "$CURRENT" != "$LAST_MODEL" ]; then
        terminal-notifier -message "Claude model changed to: $CURRENT"
        LAST_MODEL=$CURRENT
    fi
    sleep 5
done
```

## How It Works

Claude Code logs API interactions to `~/.claude/projects/PROJECT-NAME/*.jsonl`. Each entry contains a `model` field. This script:

1. Finds your current project's JSONL directory
2. Reads the most recent log entry
3. Extracts and formats the model name

## Output Format

- 🎭 Opus
- 🎵 Sonnet  
- 🌸 Haiku
- 🤖 Unknown (for unrecognized models)
- No output when not in a Claude project

## Limitations

- Only works in Claude project directories
- Shows historical data, not real-time status
- Depends on undocumented log file format
- May miss rapid model switches

## Contributing

Found a better way? Please share! This is a community workaround until Anthropic provides official support.

## License

MIT