# Debugging Guide for Boxxy Binder

## Hat Input Detection Issues

If your hat switch inputs aren't being detected, follow these steps:

### 1. Check Which Build You're Using

**Three types of builds are available:**
- **MSI Installer** (`*.msi`) - Recommended, includes all resources
- **NSIS Installer** (`*.exe` setup) - Also includes all resources
- **Standalone Executable** (`boxxy-binder.exe`) - Requires manual setup

### 2. For Standalone Executable Users

If you downloaded the standalone `.exe` file, you need to:

1. Copy `AllBinds.xml` from the repository to the same folder as `boxxy-binder.exe`
2. The app will look for `AllBinds.xml` next to the executable

**File structure should be:**
```
C:\Users\YourName\Downloads\
├── boxxy-binder.exe
└── AllBinds.xml
```

### 3. Viewing Debug Output

The app writes debug information to the console, which isn't visible in normal GUI mode.

**To see console output:**

**Method 1: Run from Command Prompt**
```cmd
cd C:\path\to\boxxy-binder
boxxy-binder.exe > debug.txt 2>&1
```

Then open `debug.txt` to see all debug output.

**Method 2: Run in Command Prompt directly**
```cmd
cd C:\path\to\boxxy-binder
boxxy-binder.exe
```

Keep the command prompt open - debug output will appear there.

### 4. What to Look For in Debug Output

When you start the app with your vjoy device connected, you should see output like:

```
[HID] Device 1 initial axes:
[HID]   axis_id=0x30 (48): X                    value=32768 range=[0, 65535] is_hat=false
[HID]   axis_id=0x31 (49): Y                    value=32768 range=[0, 65535] is_hat=false
[HID]   axis_id=0x39 (57): Hat Switch           value=    8 range=[0, 15] is_hat=true
```

**Key things to check:**

1. **Is there an axis with `is_hat=true`?**
   - If YES: The hat is detected, proceed to next steps
   - If NO: Your device might use a different HID usage ID

2. **What is the `axis_id`?**
   - Standard hats use `0x39` (57 in decimal)
   - If yours is different, note the value

3. **What is the `range`?**
   - Common ranges: `[0, 7]`, `[0, 8]`, `[0, 15]`, or `[0, 65535]`
   - The code normalizes all ranges to discrete 0-8 positions

### 5. When You Move the Hat

After initial detection, when you move the hat you should see:

```
[AXIS] Device 1: axis_id=0x39 (Hat Switch), current=0, prev=8, change=8.0, is_hat=true
[HAT] Device 1: axis_id=0x39, current_value=0, prev_value=8, logical_min=0, logical_max=15, range=15.0
[HAT] Discrete value: 0
```

**If you don't see these messages when moving the hat:**
- The device might not be reporting hat changes via HID
- Try checking Windows Game Controllers panel to verify the hat works
- The hat might be reported as buttons instead of an axis

### 6. Common Issues

**"Warning: Failed to load AllBinds.xml"**
- **Cause**: Running standalone exe without AllBinds.xml in same folder
- **Fix**: Copy AllBinds.xml to exe directory OR use MSI/NSIS installer

**"Hats not showing in Input Debugger"**
- **Cause**: Hat isn't using HID usage ID 0x39, or isn't being polled
- **Fix**: Check debug output to see if hat is in the axis list
- If present with different ID, report it on GitHub issues

**"AllBinds.xml path contains \\\\?\\"**
- **Normal**: This is Windows' extended path format
- **No action needed**: The app handles this automatically

### 7. Reporting Issues

If you're still having issues, please provide:

1. **Build type used** (MSI, NSIS, or standalone exe)
2. **Debug output** from running `boxxy-binder.exe > debug.txt`
3. **Device information** (vjoy version, configuration)
4. **Screenshot** of Windows Game Controllers showing the hat working

Post this information in a GitHub issue for assistance.

## Version Information

**Current Version**: 0.11.2
**Includes**: Hat detection fixes and enhanced logging
**Changes**: See commit history on GitHub
