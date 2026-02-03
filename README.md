# NHL Goal Celebration for Home Assistant

**Transform your home into a stadium when your favorite NHL team scores!**

Instant goal horn, lights, and TTS announcements with 97.6% reliability.

---

## 🎯 What It Does

When your NHL team scores:
- 🔊 **Goal horn plays** (45-second streaming delay)
- 💡 **Smart lights flash** in team colors
- 📢 **TTS announces** scorer, assists, time, and period
- 🏆 **Win celebration** with special light show

---

## ✨ Features

- ✅ **97.6% success rate** (tested over 40+ goals)
- ✅ **Dual-source reliability** - ESPN primary, NHL API fallback
- ✅ **Smart polling loop** - Adapts to ESPN API timing
- ✅ **Natural announcements** - "second period" not "period 2"
- ✅ **Works everywhere** - Home and away games
- ✅ **Optional debug mode** - Monitor performance without code changes
- ✅ **Light restoration** - Returns to previous state after celebration

---

## 📋 Requirements

- Home Assistant (2023.x or newer)
- ESPN sensor integration (via HACS)
- NHL API sensor integration (via HACS)
- Smart light (LIFX, Hue, etc.)
- Media player (Google Home, Chromecast, Echo)
- TTS service (Amazon Polly, Google TTS, etc.)

---

## 🚀 Quick Start

1. **Download the files:**
   - `NHLGoalScoreV34.yaml` - Main celebration script
   - `NHL_Goal_Celebration_Setup_Guide.md` - Detailed installation

2. **Follow the setup guide** for step-by-step installation

3. **Customize for your team:**
   - Change sensor names
   - Update device IDs
   - Set team colors
   - Add your team's goal horn

4. **Enjoy the game!** 🏒

---

## 📚 Documentation

**[Complete Setup Guide](NHL_Goal_Celebration_Setup_Guide.md)** - Full installation instructions with troubleshooting

---

## 🎥 How It Works

When your team's score increases, the automation:
1. Waits 45 seconds (streaming delay)
2. Polls ESPN API every 5 seconds looking for goal details
3. Falls back to NHL API if ESPN is unavailable
4. Parses scorer, assists, period, and time
5. Triggers celebrations with complete announcement

The polling loop handles ESPN's unpredictable update timing, achieving 97.6% reliability across extensive testing.

---

## 🛠️ Customization

Easily customize:
- **Team colors** - RGB values for your team
- **Goal horn** - Any YouTube video
- **Streaming delay** - Adjust for your service (30-60 seconds)
- **TTS voice** - Choose preferred voice/engine
- **Light devices** - Support for multiple brands

---

## 🏒 Tested With

- **Games:** 10+
- **Goals:** 40+
- **Success Rate:** 97.6%
- **Period:** December 2025 - January 2026
- **Status:** Production-ready (V34)

---

## 🤝 Contributing

Have improvements or adaptations for other sports? Contributions welcome!

---

## 📄 License

Free to use, modify, and share. Attribution appreciated but not required.

---

## 💡 Support

- **Issues?** Check the [Setup Guide](NHL_Goal_Celebration_Setup_Guide.md) troubleshooting section
- **Questions?** Open a GitHub issue
- **Success story?** Share it with the Home Assistant community!

---

**Bring the stadium home! Go [YOUR TEAM]!** ⚡🏒
