# NHL Goal Celebration for Home Assistant

**Transform your home into a stadium when your favorite NHL team scores!**

Goal video, flashing lights in your team's colors, and a spoken announcement of the scorer, assists, time and period, timed to match your TV stream's delay.

---

## 🎯 What It Does

When your NHL team scores:
- 🔊 **Goal video plays** on your speaker or display, about 45 seconds after the goal so it lines up with your stream
- 💡 **Smart lights flash** in team colors
- 📢 **TTS announces** the scorer, time and period, then the assists
- 🏆 **Win celebration** with a win video, light show and the final score (beta)

---

## ✨ Features

- ✅ **Two data sources** - ESPN first, NHL API as the fallback
- ✅ **Polling loop** - checks ESPN every 5 seconds until the goal details show up
- ✅ **Natural announcements** - "second period" not "period 2"
- ✅ **Assists that arrive late** - re-checks the NHL API before announcing assists
- ✅ **Works for home and away games**
- ✅ **Optional debug mode** - a notification with all the details after each goal
- ✅ **Light restoration** - the light goes back on or off the way it was
- ✅ **One settings block** - your team, light, speaker and colors are set in one place at the top of each file

---

## 📋 Requirements

- **Home Assistant 2024.10 or newer** (the YAML uses the newer `triggers:` / `actions:` format)
- **[Team Tracker](https://github.com/vasqued2/ha-teamtracker)** (ESPN data, install via HACS)
- **[hass-nhlapi](https://github.com/JayBlackedOut/hass-nhlapi)** (NHL API data, install via HACS)
- A smart light with color support (LIFX, Hue, etc.)
- A Google Home / Nest / Chromecast speaker or display (plays YouTube via cast)
- A TTS service (Amazon Polly is used here; others work with a small change)

**Tested with:** Home Assistant 2026.10.1, Team Tracker v0.18.4, hass-nhlapi v1.2.1b (pre-release, 5-second update interval), Google Home display, LIFX bulb, Amazon Polly.

---

## 📁 Files

| File | What it is |
|---|---|
| `NHL_Goal_Celebration_Script.yaml` | The main goal celebration script |
| `NHL_Goal_Automation.yaml` | Automation that starts the script when your team's score goes up |
| `NHL_Win_Celebration.yaml` | Win celebration automation (optional, beta) |
| `NHL_Goal_Celebration_Setup_Guide.md` | Step-by-step installation and customization |
| `CHANGELOG.md` | What changed in each version |

Last season's files (V34) are kept under the [`v34-2025-26-season`](https://github.com/kjdavwork/HA_NHL_Goal_Celebration/tree/v34-2025-26-season) tag.

---

## 🚀 Quick Start

1. Install Team Tracker and hass-nhlapi for your team.
2. Create the debug toggle helper (`input_boolean.nhl_goal_debug_mode`).
3. Paste `NHL_Goal_Celebration_Script.yaml` into a new script, change the settings block at the top, and save it as **NHL Goal Celebration** (entity `script.nhl_goal_celebration`).
4. Paste `NHL_Goal_Automation.yaml` into a new automation and set your team's sensor.
5. Optional: paste `NHL_Win_Celebration.yaml` into another automation.

Every place you need to change is marked **CHANGE** in the YAML. The **[Setup Guide](NHL_Goal_Celebration_Setup_Guide.md)** walks through each step.

---

## 🎥 How It Works

When your team's score goes up, the script:
1. Waits 45 seconds (streaming delay) and, at the same time, checks ESPN's latest play every 5 seconds (9 checks) for the goal details
2. Uses the NHL API if ESPN never showed the goal (this happens, for example, with some overtime winners)
3. Plays the goal video and flashes the lights for 30 seconds
4. Announces the scorer, time and period
5. Re-checks the NHL API for assists (they often show up a minute or more after the goal) and announces them

---

## 🏒 Tested With

- **V34 (2025–26 season):** 10+ games, 40+ goals, 97.6% of goals announced correctly
- **V36 (2026–27 season):** working live on 6+ goals as of October 2026
- **Win celebration V1.6:** not yet run in a real game (beta)

---

## ⚠️ Known Limitations

- **Goals close together:** a celebration takes about 2 minutes. A second goal during that time doesn't get its own celebration.
- **Opponent scores right after you:** if the other team scores within about 45 seconds, ESPN's latest play may be their goal, and the wrong scorer could be announced. Rare.
- **No clock from the NHL API:** when the NHL API fallback is used, the announcement gives the period but not the time.
- **Assists can be missed:** if the NHL API hasn't posted them about 80–90 seconds after the goal, none are announced.
- **Shootouts:** not tested.

---

## 🤝 Contributing

Have improvements or adaptations for other sports? Issues and pull requests are welcome!

---

## 📄 License

[MIT](LICENSE) - free to use, modify, and share.

---

## 💡 Support

- **Issues?** Check the [Setup Guide](NHL_Goal_Celebration_Setup_Guide.md) troubleshooting section
- **Questions?** Open a GitHub issue
- **Success story?** Share it with the Home Assistant community!

---

**Bring the stadium home! Go [YOUR TEAM]!** ⚡🏒
