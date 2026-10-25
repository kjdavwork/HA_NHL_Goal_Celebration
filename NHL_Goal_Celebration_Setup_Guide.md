# NHL Goal Celebration - User Setup Guide

Transform your home into a stadium! Get a goal video, flashing lights, and announcements when your favorite NHL team scores, timed to match your TV stream.

**Version:** goal script V36, win celebration V1.6 (beta). See [CHANGELOG.md](CHANGELOG.md).

---

## 🎯 What This Does

When your NHL team scores a goal:
1. **🔊 Goal video** - a YouTube goal horn/song plays on your speaker for 30 seconds, starting about 45 seconds after the goal (to line up with your stream)
2. **💡 Light show** - your light flashes in team colors while the video plays
3. **📢 Announcement** - "Tampa Bay Lightning goal! At 14:32 of the second period. Scored by Nikita Kucherov!" followed by "Assisted by Brandon Hagel, and Victor Hedman!"
4. **🎊 Win celebration (optional, beta)** - a win video, light show and the final score after a win

**Features:**
- ✅ Natural period names ("first period" not "period 1", "overtime")
- ✅ Two data sources (ESPN first, NHL API as the fallback)
- ✅ Assists re-checked from the NHL API, since they often show up late
- ✅ Light goes back on or off the way it was
- ✅ Optional debug notifications
- ✅ Works for home and away games

---

## 📋 Prerequisites

You need **Home Assistant 2024.10 or newer**.

### 1. Team Tracker (ESPN data)

Gives the score, game state and the latest play.

**Install:**
- HACS → search **Team Tracker** → Download → restart Home Assistant
- Settings → Devices & Services → Add Integration → **Team Tracker**
- League: **NHL**, Team ID: your team's abbreviation (for example `TB`)

Project page: https://github.com/vasqued2/ha-teamtracker

**Check it:** Developer Tools → States → find your sensor (this guide uses `sensor.lightning`). It should have attributes `team_score`, `opponent_score`, `last_play`, `quarter`, `clock` and `team_homeaway`. Its state is `PRE`, `IN` or `POST` on game days.

### 2. hass-nhlapi (NHL API data, the fallback)

Gives the scorer, assists and jersey numbers when ESPN is slow.

**Install:**
- HACS → search **NHL API** (hass-nhlapi) → Download → restart Home Assistant. If it isn't listed, add `https://github.com/JayBlackedOut/hass-nhlapi` as a custom repository (HACS → ⋮ → Custom repositories, type Integration). To get a pre-release like v1.2.1b, pick it under **Redownload → Version**.
- Settings → Devices & Services → Add Integration → **NHL API** → choose your team

Project page: https://github.com/JayBlackedOut/hass-nhlapi. Tested with v1.2.1b (a pre-release) with a 5-second update interval.

**Check it:** Developer Tools → States → find your sensor (this guide uses `sensor.lightning_nhl`). During a game it should have `scoring_player_name`, `assist1_player_name`, `goal_tracked_team`, `home_score` and `away_score`.

### 3. A smart light

Any light with color support (LIFX, Hue, etc.). This guide uses `light.lr_lifx_1`. A white-only bulb will still flash, but without team colors.

### 4. A speaker or display

A Google Home / Nest speaker or display, or a Chromecast. The goal video is played as YouTube over cast, so it needs a device that can cast YouTube. This guide uses `media_player.google_home_display`.

**Check it:** play something to it from Home Assistant first, and make sure it responds.

### 5. A TTS (text-to-speech) service

This guide uses **Amazon Polly** (voice "Matthew", neural engine), which needs an AWS account. Google Translate TTS (free, built in) and others work too; see [Using a different TTS service](#change-the-tts-service).

---

## 🛠️ Installation Steps

> **About comments:** the YAML files have comments (lines starting with `#`) that explain every setting. **Home Assistant deletes these comments when you save** in its editor. Keep the downloaded files if you want the notes later.

### Step 1: Create the Debug Toggle Helper

This turns debug notifications on and off without editing anything.

1. Go to **Settings → Devices & Services → Helpers**
2. Click **+ Create Helper** → **Toggle**
3. Name: `NHL Goal Debug Mode` (icon `mdi:hockey-puck` is optional)
4. Click **Create**, and check the entity ID is `input_boolean.nhl_goal_debug_mode`

- **Off** = silent operation
- **On** = a notification after each goal showing what data was found and used

---

### Step 2: Find Your Team's Goal Video

1. Search YouTube for your team's goal horn or goal song (for example "tampa bay lightning goal horn"). Clean audio without commentary works best.
2. Copy the **video ID**:
   - Browser: `https://www.youtube.com/watch?v=3SeeHZQVgNQ` → the ID is everything after `v=` → `3SeeHZQVgNQ`
   - Phone (Share link): `https://youtu.be/3SeeHZQVgNQ` → the ID is everything after `youtu.be/`
3. Do the same for a win video if you'll use the win celebration.

The script plays the first 30 seconds of the video (you can change that), so any length works.

---

### Step 3: Create the Goal Celebration Script

1. Go to **Settings → Automations & Scenes → Scripts**
2. Click **+ Add Script** → **Create new script**
3. Click the **⋮** menu → **Edit in YAML**
4. Delete the placeholder YAML and paste all of `NHL_Goal_Celebration_Script.yaml`
5. Change the **SETTINGS** block at the top (see the table below)
6. Change the **YouTube video ID**: search the file for `media_id` and replace `3SeeHZQVgNQ` with your video ID
7. Not using Amazon Polly? See [Change the TTS service](#change-the-tts-service)
8. Click **Save**. Keep the name **NHL Goal Celebration**.
9. Open the script's settings (⋮ → **Settings**) and check the entity ID is `script.nhl_goal_celebration`. The automations call it by that name.

**Settings block:**

| Setting | What to put there | Example |
|---|---|---|
| `espn_sensor` | Your Team Tracker sensor | `sensor.lightning` |
| `nhl_sensor` | Your hass-nhlapi sensor | `sensor.lightning_nhl` |
| `speaker` | Your speaker/display | `media_player.google_home_display` |
| `light` | Your light | `light.lr_lifx_1` |
| `team_name` | Spoken in the announcement | `Tampa Bay Lightning` |
| `team_color` | Team color as `[red, green, blue]`, 0–255 each | `[17, 0, 255]` |
| `restore_color` | Color the light goes back to if it was on | `[255, 255, 255]` |
| `stream_delay_seconds` | How far your stream is behind live | `45` |
| `goal_video_seconds` | How much of the video to play | `30` |
| `tts_voice`, `tts_engine` | Amazon Polly voice options | `Matthew`, `neural` |
| `debug_toggle` | The helper from Step 1 | `input_boolean.nhl_goal_debug_mode` |

To find an entity ID, go to **Developer Tools → States** and type part of the name in the filter box.

---

### Step 4: Create the Goal Trigger Automation

This watches your team's score and starts the script.

1. Go to **Settings → Automations & Scenes → Automations**
2. Click **+ Create Automation** → **Create new automation**
3. Click the **⋮** menu → **Edit in YAML**
4. Delete the placeholder YAML and paste all of `NHL_Goal_Automation.yaml`
5. Replace `sensor.lightning` with your Team Tracker sensor in **all 3 places** (marked `# CHANGE`)
6. Click **Save**

---

### Step 5: Create the Win Celebration (Optional, Beta)

After a win: the win video with flashing lights, then "Tampa Bay Lightning win! Final score 4 to 2. That is the game!"

1. Create another automation the same way as Step 4, and paste `NHL_Win_Celebration.yaml`
2. Replace `sensor.lightning` in the trigger
3. Change the `variables` block (same values as the script's settings)
4. Change the win video ID: search for `media_id` and replace `CLoov5iBXB4`
5. Click **Save**

If your team scores late or in overtime, the win celebration waits for the goal celebration to finish first, so they don't talk over each other.

**Beta:** V1.6 hasn't run in a real game yet. Earlier versions did, and the parts it shares with the goal script are tested.

---

## 🧪 Testing

### Before a Game

1. Turn **NHL Goal Debug Mode** on.
2. Check your sensors in **Developer Tools → States**.
3. Optional manual test: **Developer Tools → Actions** → `script.nhl_goal_celebration` → **Perform action**. You should get:
   - about 45 seconds of nothing (the delay and the ESPN check)
   - the goal video and flashing lights for 30 seconds
   - possibly an announcement: if the NHL API still has your team's last goal and the score matches, it announces that goal. Otherwise there's no announcement.
   - the debug notification at the end (about 1½ to 2 minutes after starting)

### During a Game

With debug mode on, after each goal you should hear the video and announcements and see a **Goal Debug - V36** notification. An example (shortened):

```
=== DATA SOURCE: ESPN ===

ESPN Loop Results:
- Data captured: ESPN
- Iteration: 4
- Raw last_play: Brandon Hagel Goal (5) Wrist Shot, assists: none

NHL API Fallback Check:
- Has scorer: True
- Is our team's goal: True
- Fallback valid: True
- Fallback used: False

Final Goal Data:
- Scorer: Brandon Hagel
- Period: 1 (first period)
- Clock: 13:30

Assists:
- Has assists: True
- Assist 1: Brayden Point (#21)
- Assist 2: Jake Guentzel (#59)
- NHL re-read: Brayden Point, Jake Guentzel (NHL score 1, waited 6 s)
```

- **DATA SOURCE** tells you where the scorer came from: `ESPN`, `NHL_API` (the fallback) or `none` (nothing found, so no announcement).
- **Iteration** is which 5-second ESPN check first found the goal (1–9).
- **NHL re-read** shows the assists found when the NHL API was checked again, and how long it waited for them.

Once everything works, turn debug mode **off** if you don't want the notifications.

---

## 🎛️ Customization

### Team colors

Set `team_color` in the script and the win celebration:

```yaml
# Tampa Bay Lightning (blue)
team_color: [17, 0, 255]

# Toronto Maple Leafs (blue)
team_color: [0, 32, 91]

# Montreal Canadiens (red)
team_color: [175, 30, 45]

# Boston Bruins (gold)
team_color: [252, 181, 20]

# New York Rangers (blue)
team_color: [0, 56, 168]

# Detroit Red Wings (red)
team_color: [206, 17, 38]
```

Very dark colors (like the Leafs' navy) can look dim on a bulb; a brighter shade of the same color often looks better.

### Goal video

Search the script for `media_id` and replace the video ID (Step 2). Change `goal_video_seconds` to play more or less of it.

### Streaming delay

Change `stream_delay_seconds` to match how far your TV or stream is behind live (30–60 seconds is typical). Start with 45 and adjust after a game or two: if the video starts before you see the goal on TV, make it longer.

The ESPN check always runs for 45 seconds whatever this is set to, and the announcement comes only after both the check and the video have finished.

### Change the TTS service

The script and win celebration use `tts.amazon_polly_say`. To use another service, search each file for `amazon_polly_say` (2 places in the script, 1 in the win celebration). For each one:
- change the action, for example to `tts.google_translate_say`
- delete the `options:` lines under it (voice and engine are Amazon Polly options)

Amazon Polly voices to try: **Matthew** (male), **Joanna** (female), **Joey** (male), all with `engine: neural`.

### More than one light

Set `light` to a light group (Settings → Devices & Services → Helpers → Create Helper → Group → Light group) to flash several lights together.

---

## 🔧 Troubleshooting

### No celebration at all

1. **Is a game on?** The automation only runs while your Team Tracker sensor's state is `IN` (or just after, `POST`).
2. **Is the automation on?** Settings → Automations → check it isn't disabled.
3. **Does the script's entity ID match?** It must be `script.nhl_goal_celebration` (Step 3, item 9).
4. **Check the trace:** Settings → Automations → your automation → ⋮ → **Traces** shows each run and why it stopped.
5. **Check the logs:** Settings → System → Logs.

### Video plays but no announcement

1. Turn debug mode on and look at the notification. `DATA SOURCE: none` means neither ESPN nor the NHL API had the goal details in time.
2. Test the TTS service by itself: Developer Tools → Actions → `tts.amazon_polly_say` (or yours).
3. Make sure the speaker is online.

### Announcement is cut off

Make sure you're on V36 (the debug title says "Goal Debug - V36"). Earlier versions turned the speaker off after a fixed 10 seconds.

### No assists announced

Unassisted goals have none, of course. Otherwise, the NHL API sometimes posts assists more than 80–90 seconds after the goal, which is later than the script waits. The **NHL re-read** line in the debug notification shows what it found.

### "period 2" instead of "second period"

You're on a version older than V34. Update to the current script.

### Lights don't flash

1. Check `light` in the settings matches your light's entity ID.
2. Check the light is powered and responds in Home Assistant.
3. White-only bulbs flash but don't show team colors.

### Second goal not celebrated

A celebration takes about 2 minutes, and a new goal during that time is skipped (see Known Limitations below).

---

## 📊 How It Works

**The challenge:** ESPN's `last_play` updates at unpredictable times. Sometimes it shows the goal right away, sometimes after 30+ seconds, and sometimes (for example some overtime winners) never.

**The solution:** check repeatedly while waiting for the stream delay, with the NHL API as a backup.

1. Your team's ESPN `team_score` goes up → the automation starts the script.
2. Two things run at the same time:
   - **the stream delay** (45 s), then the goal video and flashing lights (30 s)
   - **the ESPN check**: 9 checks of `last_play`, 5 seconds apart (45 s). It always runs all 9. Each one that finds "Goal" updates the play text, but the clock and period are kept from the **first** one, so the announced time is when the goal happened.
3. If ESPN never showed the goal, the **NHL API** is used, but only if it says the goal was your team's and its score matches ESPN's.
4. The video stops and the scorer is announced. The script waits for the speech to finish before turning the speaker off.
5. If there are no assists yet, it waits up to 20 seconds for the NHL API to post them, checks again, and announces them.
6. The debug notification is sent (if debug mode is on).

---

## ⚠️ Known Limitations

- **Goals close together:** a celebration takes about 2 minutes, and a second goal during that time doesn't get its own celebration. (The automation's `mode: single` also stops a score that briefly flickers from being celebrated twice.)
- **Opponent scores right after you:** if the other team scores within about 45 seconds, ESPN's latest play may be their goal and the wrong scorer could be announced. Rare.
- **No clock from the NHL API:** when the fallback is used, the announcement gives the period but not the time.
- **Late assists:** assists posted more than about 80–90 seconds after the goal are missed.
- **Shootouts:** not tested.
- **Playoff overtime:** periods 5 and 6 are announced as "second overtime" and "third overtime"; later ones as "period 7" and so on.

---

## 📄 License

MIT - see [LICENSE](LICENSE). Free to use, modify, and share.

**Support:** If this brings stadium excitement to your home, share your success story with the HA community!

---

## 🏒 GO [YOUR TEAM]! ⚡

Enjoy the celebrations! 🎊
