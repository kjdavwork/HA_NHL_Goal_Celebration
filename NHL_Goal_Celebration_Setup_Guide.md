# NHL Goal Celebration - User Setup Guide

Transform your home into a stadium! Get instant goal horn, lights, and announcements when your favorite NHL team scores.

**Success Rate:** 97.6% goal detection reliability  
**Tested Over:** 10+ games, 40+ goals  
**Status:** Production-ready (V34)

---

## 🎯 What This Does

When your NHL team scores a goal:
1. **🔊 Goal Horn** - YouTube audio plays (45-second delay for streaming)
2. **💡 Light Show** - Team color lights flash 5 times
3. **📢 TTS Announcement** - "Tampa Bay Lightning goal! At 14:32 of the second period. Scored by number 86, Nikita Kucherov! Assisted by number 77, Victor Hedman and number 88, Andrei Vasilevskiy!"
4. **🎊 Win Celebration** - Special blue light show after wins

**Features:**
- ✅ Natural period names ("first period" not "period 1")
- ✅ Dual-source reliability (ESPN + NHL API fallback)
- ✅ Smart light restoration (returns to previous state)
- ✅ Optional debug mode (for monitoring)
- ✅ Works for home and away games
- ✅ Handles assisted and unassisted goals

---

## 📋 Prerequisites

### Required Home Assistant Components:

#### 1. **ESPN Sensor Integration**
You need the ESPN sensor tracking your NHL team's games.

**Installation:**
- HACS → Integrations → Search "ESPN" → Install
- Configuration → Integrations → Add Integration → ESPN
- Select "NHL" and your team (e.g., "Tampa Bay Lightning")

**Entity Created:** `sensor.lightning` (or your team name)

**Verify It Works:**
- Developer Tools → States
- Find `sensor.lightning`
- Check attributes include: `team_score`, `last_play`, `quarter`, `clock`

---

#### 2. **NHL API Sensor Integration** (Fallback)
Provides backup data if ESPN is slow/incorrect.

**Installation:**
- HACS → Integrations → Search "NHL API" → Install
- Configuration → Integrations → Add Integration → NHL API
- Select your team

**Entity Created:** `sensor.lightning_nhl` (or your team name + _nhl)

**Verify It Works:**
- Developer Tools → States
- Find `sensor.lightning_nhl`
- Check attributes include: `scoring_player_name`, `assist1_player_name`, etc.

---

#### 3. **Smart Light** (LIFX, Hue, etc.)
At least one smart light for the celebration light show.

**This guide uses LIFX:** `light.lr_lifx_1`

**If using different lights:**
- You'll need to update the entity_id in the script
- RGB color support recommended (for team colors)

---

#### 4. **Media Player** (Google Home, Chromecast, Echo)
For playing goal horn and TTS announcements.

**This guide uses Google Home:** `media_player.google_home_display`

**Supported:**
- Google Home / Nest speakers
- Chromecast devices
- Echo devices (with workarounds)

**Verify It Works:**
- Try playing something manually to the device
- Make sure it's on your network and responsive

---

#### 5. **TTS Service** (Text-to-Speech)
For announcing goal details.

**This guide uses Amazon Polly:**
- Configuration → Integrations → Add Integration → Amazon Polly
- Requires AWS account (free tier available)
- Voice used: "Matthew" (Neural engine)

**Alternatives:**
- Google TTS (free, built-in)
- Microsoft TTS
- Any TTS service HA supports

---

## 🛠️ Installation Steps

### Step 1: Create the Debug Toggle Helper

This allows you to turn debug notifications on/off without editing code.

**Instructions:**
1. Go to **Settings → Devices & Services → Helpers**
2. Click **"+ CREATE HELPER"**
3. Select **"Toggle"**
4. Configure:
   - **Name:** `NHL Goal Debug Mode`
   - **Icon:** `mdi:hockey-puck` (optional)
   - **Entity ID:** `input_boolean.nhl_goal_debug_mode`
5. Click **"CREATE"**

**Usage:**
- **OFF** (default) = Silent operation, no debug notifications
- **ON** = Shows detailed debug info for each goal

---

### Step 2: Find Your Team's Goal Horn Video

Before creating the script, find your team's goal horn on YouTube.

**Instructions:**

1. **Search YouTube** for your team's goal horn:
   - Example searches: 
     - "tampa bay lightning goal horn"
     - "toronto maple leafs goal horn 2025"
     - "boston bruins goal horn official"
   - Look for clean audio versions (not gameplay clips with commentary)
   - Official team channels or high-quality fan uploads work best

2. **Find the Video ID:**
   
   **Desktop/Browser Method:**
   - Open the video on YouTube
   - Look at the URL in your browser's address bar
   - Example: `https://www.youtube.com/watch?v=MBxxCDtBXlY`
   - The Video ID is everything after `v=` → **MBxxCDtBXlY**
   
   **Mobile App Method:**
   - Open the video in YouTube app
   - Tap "Share"
   - The share URL will look like: `https://youtu.be/MBxxCDtBXlY`
   - The Video ID is everything after `youtu.be/` → **MBxxCDtBXlY**

3. **Copy the Video ID** - you'll need it in Step 3 (line 20 of the script)

**Example Video IDs by Team:**
- Tampa Bay Lightning: `MBxxCDtBXlY`
- (Search for your team and extract the ID using the method above)

**Tips for Finding Good Goal Horn Videos:**
- Search for "official" or "HD" versions
- Look for videos that are 10-30 seconds long (just the horn)
- Avoid highlight reels or gameplay videos
- Test the video before using - make sure it sounds right!

---

### Step 3: Create the Goal Celebration Script

**Instructions:**
1. Go to **Settings → Automations & Scenes → Scripts tab**
2. Click **"+ ADD SCRIPT"**
3. Click the **⋮ menu → Edit in YAML**
4. Delete the placeholder YAML
5. **Copy/paste the entire V34 script** (NHLGoalScoreV34.yaml)

**Important Customizations:**

Before saving, update these lines to match YOUR devices:

**Line 17:** Your Google Home/Chromecast entity
```yaml
entity_id: media_player.google_home_display  # ← Change this
```

**Lines 23, 35, 47, 53, 59, 67, 79, 85:** Your light entity
```yaml
entity_id: light.lr_lifx_1  # ← Change this (appears multiple times)
```

**Line 20:** Goal horn YouTube video (optional - change to your team's horn)
```yaml
media_content_id: "{\"app_name\": \"youtube\", \"media_id\": \"MBxxCDtBXlY\"}"
                                                              # ↑ Video ID
```

**Lines 24-26, 40-44:** Team colors (optional - change RGB values)
```yaml
rgb_color:
  - 17    # Red (0-255)
  - 0     # Green (0-255)
  - 255   # Blue (0-255)
```

**Lines 292, 302:** Your team name in TTS
```yaml
Tampa Bay Lightning goal!  # ← Change to your team
```

**Line 297:** TTS voice/engine (if using different TTS)
```yaml
voice: Matthew
engine: neural
```

**Lines 327, 341:** Media player device_id (for turn_off commands)
- Go to Settings → Devices → Find your Google Home
- Copy the device ID from the URL
- Replace `e1fdc7415c5e91f01e3d4ab7d05add84`

6. Click **"SAVE"**
7. Script name should be: **NHLGoalScoreV34**

---

### Step 4: Create the Automation (Trigger)

This watches for score changes and calls the script.

**Instructions:**
1. Go to **Settings → Automations & Scenes → Automations tab**
2. Click **"+ CREATE AUTOMATION"**
3. Click **"Start with an empty automation"**
4. Click the **⋮ menu → Edit in YAML**
5. Copy/paste this automation:

```yaml
alias: Lightning Score V34 Production
description: Trigger goal celebration when Lightning scores
triggers:
  - entity_id: sensor.lightning
    attribute: team_score
    trigger: state
    enabled: true
conditions:
  - condition: template
    value_template: |-
      {{ trigger.to_state.attributes.team_score | int > 
         trigger.from_state.attributes.team_score | int }}
  - condition: or
    conditions:
      - condition: state
        entity_id: sensor.lightning
        state: IN
      - condition: state
        entity_id: sensor.lightning
        state: POST
actions:
  - action: script.nhlgoalscorev34
    data: {}
mode: single
```

**Important Customizations:**

**Lines 4, 16:** Your ESPN sensor entity
```yaml
entity_id: sensor.lightning  # ← Change to your team's sensor
```

**Line 23:** Script name (must match Step 3)
```yaml
action: script.nhlgoalscorev34  # ← Must match your script name
```

6. Click **"SAVE"**

---

### Step 5: Create the Win Celebration (Optional)

Triggers a special blue light celebration after your team wins.

**Instructions:**
1. Go to **Settings → Automations & Scenes → Automations tab**
2. Click **"+ CREATE AUTOMATION"**
3. Click **"Start with an empty automation"**
4. Click the **⋮ menu → Edit in YAML**
5. Copy/paste the Win Celebration automation YAML (if you have it)

**Note:** The win celebration was mentioned as "V1.4" but the specific YAML wasn't provided in this session. If you have it from the previous conversation, paste it here. Otherwise, the goal celebration is the main feature.

---

## 🧪 Testing

### Verify Setup (Before a Game):

**1. Check Debug Toggle:**
- Settings → Devices & Services → Helpers
- Find "NHL Goal Debug Mode"
- Toggle it ON (for testing)

**2. Check Your Sensors:**
- Developer Tools → States
- Find `sensor.lightning` (or your team)
- Verify it shows game data when there's a game

**3. Manual Test (Optional):**
- Developer Tools → Services
- Service: `script.nhlgoalscorev34`
- Click "CALL SERVICE"
- You should see/hear:
  - 45-second delay (ESPN polling loop running)
  - Goal horn plays
  - Lights flash blue
  - Debug notification (if toggle is ON)
  - No TTS (because no real goal data)

---

### During a Game:

**First Goal Test:**
1. Make sure debug mode is **ON**
2. Wait for your team to score
3. You should see:
   - Goal horn after ~45 seconds
   - Lights flash in team colors
   - TTS announces scorer, assists, period
   - Debug notification with all data

**What the Debug Shows:**
```
=== DATA SOURCE: ESPN ===
ESPN Loop Results:
- Data captured: ESPN
- Iteration: 6
- Raw last_play: Nikita Kucherov Goal (20) Wrist Shot, assists: Steven Stamkos (15), Victor Hedman (30)

NHL API Fallback Check:
- Has scorer: True
- Is Lightning goal: True
- Fallback used: False

Final Goal Data:
- Scorer: Nikita Kucherov
- Period: 2 (second period)
- Clock: 14:32
- Assists: Steven Stamkos, Victor Hedman
```

**If Everything Works:** Turn debug mode **OFF** for silent operation!

---

## 🎛️ Customization Options

### Change Team Colors:

Edit the script RGB values (lines 24-26, 40-44):

**Popular NHL Team Colors:**
```yaml
# Tampa Bay Lightning (Blue)
rgb_color: [17, 0, 255]

# Toronto Maple Leafs (Blue)
rgb_color: [0, 32, 91]

# Montreal Canadiens (Red)
rgb_color: [175, 30, 45]

# Boston Bruins (Gold/Yellow)
rgb_color: [252, 181, 20]

# New York Rangers (Blue)
rgb_color: [0, 56, 168]

# Detroit Red Wings (Red)
rgb_color: [206, 17, 38]
```

---

### Change Goal Horn:

To use a different goal horn video (see Step 2 for finding videos):

1. Find your preferred goal horn video on YouTube
2. Extract the Video ID from the URL (see Step 2 instructions)
   - Example: `https://www.youtube.com/watch?v=ABC123XYZ`
   - Video ID is: `ABC123XYZ`

3. Update line 20 in the script:
```yaml
media_content_id: "{\"app_name\": \"youtube\", \"media_id\": \"YOUR_VIDEO_ID\"}"
```

Replace `YOUR_VIDEO_ID` with the ID you copied.

---

### Adjust Streaming Delay:

If your streaming service has more/less delay than 45 seconds:

**Line 14 in script:**
```yaml
- delay:
    seconds: 45  # ← Change this (30-60 typical range)
```

---

### Change TTS Voice:

If using Amazon Polly, available voices:
- **Matthew** (Male, Neural) - Used in this guide
- **Joanna** (Female, Neural)
- **Joey** (Male, Neural)

Update lines 297-298:
```yaml
voice: Matthew  # ← Change this
engine: neural
```

---

## 🔧 Troubleshooting

### No Celebration Happens:

**Check these:**
1. **Is there a game today?**
   - Sensor won't trigger without active game
   - Check: Developer Tools → States → `sensor.lightning`

2. **Is the automation enabled?**
   - Settings → Automations → Find your automation
   - Make sure it's not disabled

3. **Does the script exist?**
   - Settings → Scripts → Look for "NHLGoalScoreV34"

4. **Check the logs:**
   - Settings → System → Logs
   - Look for errors mentioning your script/automation

---

### Horn Plays But No TTS:

**Possible causes:**
1. **Script couldn't find goal data**
   - Enable debug mode to see what data was captured
   - Check if `goal_scorer` = "Unknown"

2. **TTS service not configured**
   - Verify Amazon Polly (or your TTS) is set up
   - Test TTS manually: Developer Tools → Services → `tts.amazon_polly_say`

3. **Media player unavailable**
   - Check if Google Home is online
   - Try playing something else to it

---

### Wrong Period Announced:

If you hear "period 2" instead of "second period":
- You might be running V33 instead of V34
- Verify your script has the `| trim` filters on lines 112, 183, and 188

---

### Lights Don't Flash:

**Check:**
1. **Light entity ID correct?**
   - Verify line 23: `entity_id: light.lr_lifx_1` matches YOUR light

2. **Light supports RGB?**
   - Some smart bulbs are white-only
   - Script requires color support

3. **Light is powered on?**
   - Physical power switch must be on
   - HA can't control unpowered lights

---

## 📊 How It Works (ESPN Polling Loop)

**The Challenge:** ESPN updates their `last_play` field at unpredictable speeds. Sometimes it shows the goal immediately, sometimes it takes 30+ seconds.

**The Solution:** A polling loop that checks repeatedly until the goal data appears.

**How it works:**
1. When `team_score` increases (goal scored), automation triggers
2. Script waits 45 seconds for streaming delay
3. Meanwhile, the polling loop checks ESPN's `last_play` every 5 seconds:
   - **Iteration 1:** Check for "Goal" text → Not found, wait 5 seconds
   - **Iteration 2:** Check again → Not found, wait 5 seconds
   - **Iteration 3:** Check again → **Found!** → Capture data, exit loop
4. If ESPN never shows the goal (rare), NHL API provides fallback data
5. Parse scorer, assists, period, time
6. Play celebrations with captured data

**Result:** 97.6% success rate across 40+ goals tested!

---

## 🎉 Success Rate Data

**Tested:** December 2025 - January 2026  
**Games:** 10+  
**Goals:** 40+  
**Success Rate:** 97.6%

**Typical Results:**
- **Iteration 0-6:** ESPN found goal quickly (most common)
- **Iteration 7-9:** ESPN was slow but polling loop caught it
- **NHL API Fallback:** Used when ESPN completely failed

**Known Limitations:**
- **Shootout goals:** Don't trigger (NHL rule: shootouts aren't "goals")
- **Rapid goals:** < 10 seconds apart may queue celebrations

---

## 📅 Future Plans

Before NHL Playoffs (April 2026):
- Research playoff overtime rules (unlimited 20-min periods)
- Test automation behavior during playoff OT
- Create Home Assistant Blueprint
- Share with community on GitHub

---

## 📄 License

Feel free to use, modify, and share! Attribution appreciated but not required.

**Support:** If this brings stadium excitement to your home, consider sharing your success story with the HA community!

---

## 🏒 GO [YOUR TEAM]! ⚡

Enjoy the celebrations! 🎊
