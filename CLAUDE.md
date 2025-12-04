# CLAUDE.md - AI Assistant Guide for Audio-To-Sign-Language-Translator

## Project Overview

This is an **Audio-To-Sign-Language-Translator** application that converts spoken English audio into Indian Sign Language (ISL) visual representations. The system uses speech recognition to capture voice input, processes it using NLP techniques, and displays corresponding sign language animations (GIFs) for complete phrases or static images for individual letters.

**Primary Purpose**: Assist hearing-impaired individuals by translating real-time audio into visual sign language representations.

## Technology Stack

- **Language**: Python 3.6+
- **Speech Recognition**: Google Speech Recognition API
- **GUI Framework**: EasyGUI (main menu), Tkinter (GIF display)
- **Image Processing**: PIL (Pillow), OpenCV, NumPy
- **Visualization**: Matplotlib
- **Audio Input**: PyAudio (microphone access)

## Repository Structure

```
Audio-To-Sign-Language-Translator/
├── main.py                 # Main application entry point (134 lines)
├── requirements.txt        # Python dependencies
├── README.md              # User-facing documentation
├── LICENSE                # Apache 2.0 license
├── .gitignore            # Standard Python .gitignore
├── ISL_Gifs/             # Sign language GIF animations (86 files)
│   └── *.gif             # Animated phrases/sentences (e.g., "good morning.gif")
└── letters/              # Individual letter images (26 files)
    └── *.jpg             # Static hand signs for a-z (lowercase filenames)
```

### Key Directories

1. **ISL_Gifs/** (86 GIF files)
   - Contains animated sign language representations for complete phrases
   - Examples: "good morning.gif", "hello what is your name.gif", "all the best.gif"
   - File naming: Uses lowercase, spaces in filenames match phrase text

2. **letters/** (26 JPG files)
   - Contains static images for individual letter fingerspelling (a-z)
   - File naming: Single lowercase letter (e.g., "a.jpg", "b.jpg")
   - Used as fallback when phrase not found in ISL_Gifs

## Architecture & Data Flow

### Application Flow

```
User Interface (EasyGUI)
    ↓
[Live Voice Button] → Microphone Input (PyAudio)
    ↓
Speech Recognition (Google API)
    ↓
Text Preprocessing (remove punctuation)
    ↓
Phrase Matching Logic
    ↓
    ├─→ [Match in isl_gif list] → Display GIF (Tkinter window)
    └─→ [No match] → Spell character-by-character (Matplotlib)
```

### Core Function: `func()` (main.py:13-121)

The main processing function handles:
1. **Audio Capture**: Continuous microphone listening with ambient noise adjustment
2. **Speech-to-Text**: Google Speech Recognition API conversion
3. **Text Normalization**: Removes punctuation, converts to lowercase
4. **Phrase Lookup**: Checks if recognized text matches predefined ISL phrases
5. **Display Logic**:
   - **Phrase match**: Opens Tkinter window with animated GIF
   - **No match**: Displays individual letter images using Matplotlib (0.8s per letter)
6. **Exit Condition**: Says "goodbye" to terminate

## Key Components

### 1. ISL Phrase Database (main.py:15-34)

Hardcoded list of 200+ supported ISL phrases stored in `isl_gif` array:
- Common greetings: "good morning", "good night", "hello what is your name"
- Questions: "are you hungry", "what is your age", "where do you stay"
- Locations: "delhi", "mumbai", "nagpur", "australia"
- General phrases: "all the best", "take care", "be careful"

**Important**: This list must match the filenames in `ISL_Gifs/` directory (case-insensitive).

### 2. ImageLabel Class (main.py:61-95)

Custom Tkinter Label widget for animated GIF playback:
- Loads GIF frames using PIL
- Automatically loops animation
- Extracts frame delay from GIF metadata (default: 100ms)

### 3. Letter Display Logic (main.py:104-116)

Fallback mechanism for unrecognized phrases:
- Iterates through each character
- Displays corresponding letter image if in alphabet (a-z)
- Uses Matplotlib with 0.8s pause between letters
- Skips non-alphabetic characters

## Development Setup

### Prerequisites

```bash
# Python 3.6 or higher required
python --version

# Install dependencies
pip install -r requirements.txt
```

### Required Dependencies

```
SpeechRecognition==3.8.1
numpy==1.18.2
opencv-python==4.2.0.34
```

**Note**: Additional dependencies imported but not in requirements.txt:
- PIL/Pillow
- easygui
- matplotlib
- pyaudio (required for microphone access)

### Running the Application

```bash
python main.py
```

**User Flow**:
1. GUI window appears with "HEARING IMPAIRMENT ASSISTANT" title
2. Click "Live Voice" button to start recording
3. Speak into microphone
4. View sign language output (GIF or letter sequence)
5. Say "goodbye" to stop recording
6. Click "All Done!" to exit application

## Known Issues & Important Considerations

### Critical Path Issues

1. **Hardcoded Windows Path** (main.py:100)
   ```python
   lbl.load(r'D:\min5\Automatic-Indian-Sign-Language-Translator-master\ISL_Gifs\{0}.gif'.format(a.lower()))
   ```
   - **CRITICAL BUG**: This will fail on non-Windows systems or different directory structures
   - Should use: `os.path.join('ISL_Gifs', f'{a.lower()}.gif')`
   - Blocks GIF display functionality entirely

2. **Missing Resource Files**
   - `signlang.png` referenced in main.py:124 but not in repository
   - Application may fail to launch without this image file

3. **Incomplete Letter Set**
   - Only 26 letters in letters/ directory (no uppercase variants)
   - Some letters mentioned in ISL_Gifs directory (A.jpg, B.jpg) not in letters/

4. **Unused Import/Reference** (main.py:133)
   - References `selecting.py` which doesn't exist in repository
   - Third button option is non-functional

### Design Considerations

1. **No NLP Processing**: Despite README mention, no actual NLP text preprocessing beyond punctuation removal
2. **No Error Handling**: Speech recognition failures print "Could not listen" but continue
3. **Blocking UI**: Matplotlib display blocks thread, preventing concurrent operations
4. **Phrase List Maintenance**: 200+ phrase array is hardcoded and difficult to maintain

## Conventions to Follow

### Code Style

1. **Indentation**: Mix of spaces and tabs (standardize to 4 spaces recommended)
2. **Naming Conventions**:
   - Functions: lowercase with underscores (e.g., `func()`)
   - Variables: lowercase (e.g., `isl_gif`, `arr`)
   - Classes: PascalCase (e.g., `ImageLabel`)

3. **Comments**: Minimal comments; add documentation for complex logic

### File Naming

1. **GIF Files**: Must exactly match phrases in `isl_gif` list (case-insensitive)
   - Use lowercase
   - Preserve spaces in filenames
   - Format: `{phrase}.gif`

2. **Letter Images**: Single lowercase letter + .jpg
   - Format: `{letter}.jpg` (e.g., "a.jpg", "z.jpg")

### Path Handling

- **ALWAYS** use relative paths from project root
- **ALWAYS** use `os.path.join()` for cross-platform compatibility
- **NEVER** hardcode absolute paths

## Common Development Tasks

### Adding New ISL Phrases

1. **Add GIF file** to `ISL_Gifs/` directory
   - Filename must be lowercase phrase (e.g., "thank you.gif")

2. **Update phrase list** in main.py:15-34
   ```python
   isl_gif = ['existing phrases', 'new phrase here']
   ```

3. **Test recognition**: Say the phrase clearly into microphone

### Fixing the Path Issue

**Critical fix needed in main.py:100**:

```python
# Current (BROKEN):
lbl.load(r'D:\min5\Automatic-Indian-Sign-Language-Translator-master\ISL_Gifs\{0}.gif'.format(a.lower()))

# Should be:
gif_path = os.path.join('ISL_Gifs', f'{a.lower()}.gif')
lbl.load(gif_path)
```

### Improving Speech Recognition

1. **Adjust noise threshold**: Modify `r.adjust_for_ambient_noise(source)` parameters
2. **Add language support**: Pass language parameter to `recognize_google(audio, language='en-IN')`
3. **Implement timeout**: Add timeout to `r.listen(source, timeout=5)`

### Extending Letter Display

To add support for numbers or special characters:

1. Add images to `letters/` directory (e.g., "0.jpg", "1.jpg")
2. Update character array in main.py:37-38:
   ```python
   arr = ['a','b',...,'z','0','1',...,'9']
   ```

## Testing Considerations

### Manual Testing Checklist

- [ ] Microphone access works on target platform
- [ ] Speech recognition accurately captures phrases
- [ ] GIF files display correctly for all supported phrases
- [ ] Letter images display for unknown phrases
- [ ] "goodbye" command exits recording loop
- [ ] GUI buttons respond correctly
- [ ] No crashes on malformed audio input

### Platform-Specific Testing

- **Windows**: Check PyAudio microphone access permissions
- **Linux**: Verify ALSA/PulseAudio configuration
- **macOS**: Confirm microphone permissions in System Preferences

## Dependencies Deep Dive

### SpeechRecognition (3.8.1)
- Provides `sr.Recognizer()` and `sr.Microphone()`
- Supports multiple speech recognition engines
- Uses Google Speech Recognition API (requires internet)

### OpenCV (4.2.0.34)
- Currently imported but not actively used in main logic
- May be intended for future video processing features

### NumPy (1.18.2)
- Used for image array conversion: `np.asarray()`
- Required by matplotlib for image display

### Additional Required (not in requirements.txt)
```
Pillow>=8.0.0          # Image.open(), ImageTk
easygui>=0.98.1        # buttonbox()
matplotlib>=3.0.0      # plt.imshow(), plt.pause()
pyaudio>=0.2.11        # Microphone audio capture
```

## Security & Privacy Considerations

1. **Network Dependency**: Google Speech API sends audio data to external servers
2. **Microphone Access**: Requires user permission on most platforms
3. **No Data Storage**: Audio not saved locally (privacy-friendly)
4. **No Authentication**: Application runs locally without user accounts

## Future Enhancement Opportunities

1. **Offline Speech Recognition**: Replace Google API with local model (e.g., Vosk)
2. **Dynamic Phrase Loading**: Load phrases from JSON/database instead of hardcoded array
3. **Multi-language Support**: Add regional language support beyond English
4. **Video Output**: Generate sign language videos from sentences
5. **Mobile App**: Port to Android/iOS using Kivy or React Native
6. **Web Interface**: Create Flask/Django web application
7. **Real NLP**: Implement actual grammar analysis and sentence structure handling

## Git Workflow

### Branch Naming
- Feature branches: `claude/feature-description-{session-id}`
- All development should occur on claude/* branches

### Commit Guidelines
- Use descriptive commit messages
- Reference issue numbers if applicable
- Test before committing

### Common Git Commands
```bash
# Check current branch
git branch

# Create and switch to new branch
git checkout -b claude/feature-name-sessionid

# Stage and commit changes
git add .
git commit -m "Descriptive message"

# Push to remote
git push -u origin claude/feature-name-sessionid
```

## Troubleshooting

### "Could not listen" Error
- **Cause**: Microphone not accessible or ambient noise too high
- **Solution**: Check microphone permissions, reduce background noise

### GIF Not Displaying
- **Cause**: Path issue (main.py:100) or missing GIF file
- **Solution**: Fix hardcoded path, verify GIF exists in ISL_Gifs/

### Matplotlib Window Freezing
- **Cause**: Blocking display calls
- **Solution**: Use `plt.draw()` and `plt.pause()` instead of `plt.show()`

### Import Errors
- **Cause**: Missing dependencies
- **Solution**: Run `pip install -r requirements.txt` and install additional packages

## Resources

- **Repository**: Audio-To-Sign-Language-Translator
- **License**: Apache 2.0
- **Python Version**: 3.6+
- **Platform**: Cross-platform (Windows/Linux/macOS with platform-specific fixes)

## Quick Reference

### Main Entry Point
- File: `main.py`
- Function: `func()` (speech recognition loop)
- UI: EasyGUI buttonbox (line 123-133)

### Key Arrays
- `isl_gif`: Supported ISL phrases (200+ entries)
- `arr`: Supported characters for fingerspelling (a-z)

### Display Methods
- **GIF**: Tkinter window with ImageLabel class
- **Letters**: Matplotlib figure with 0.8s display per character

### Exit Conditions
- Voice: Say "goodbye"
- GUI: Click "All Done!" button

---

**Last Updated**: 2025-12-04
**Codebase Version**: Based on commit 6481f01
