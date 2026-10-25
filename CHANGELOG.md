# Changelog

## Goal script V36 / Win celebration V1.6 (October 2026)

Last season's files are kept under the [`v34-2025-26-season`](https://github.com/kjdavwork/HA_NHL_Goal_Celebration/tree/v34-2025-26-season) tag.

### Files renamed
- `NHLGoalScoreV34.yaml` → `NHL_Goal_Celebration_Script.yaml`
- `NHLWinCelebration.yaml` → `NHL_Win_Celebration.yaml`
- The script is now **NHL Goal Celebration** (`script.nhl_goal_celebration`), without a version number, so the automations don't need to change with each new version. If you used V34, update your automation to call the new name.

### Goal script (V35 and V36)
- **Announcements no longer cut off.** The fixed 10-second delay before turning the speaker off is replaced by waiting for the speech to start and then finish. A brief "buffering" no longer counts as finished.
- **Assists announced much more often.** ESPN rarely has assists in time, and the NHL API often posts them a minute or more after the goal. Just before the assist announcement, the script waits up to 20 seconds for NHL assists and reads them (names and numbers) again.
- **Correct clock.** The ESPN check now keeps the clock and period from the first time it sees the goal. It used to keep the last one, so the announced time could be several seconds late.
- **Longer goal video.** It plays for `goal_video_seconds` (30 by default), timed by the clock rather than a fixed number of light flashes.
- **Settings block.** Sensors, speaker, light, team name, colors, delay, video length and TTS voice are set in one place at the top. Every place to change is marked `CHANGE`.
- The speaker is turned off by entity ID, so you no longer need to look up its device ID.
- The debug notification moved to the end and adds an "NHL re-read" line showing what the assist re-check found.

### Goal automation
- Calls `script.nhl_goal_celebration`.

### Win celebration (V1.5 and V1.6, beta)
- **Trigger changed** to the game going from in progress to final, so overtime and shootout wins count. (V1.4 waited for the clock to say exactly "Final".)
- **Waits for the goal celebration to finish** (up to 3 minutes), so a late or overtime winner and the win celebration don't overlap.
- **Win video** plays on the speaker for `win_video_seconds` (45 by default) while the lights flash, then the final score is announced.
- Same speech wait fix and settings block as the goal script.
- Not yet run in a real game.

## Goal script V34 (January 2026)
- Fixed the period name comparison ("period 2" instead of "second period").
- ESPN polling loop with NHL API fallback.
- 97.6% of goals announced correctly over 10+ games and 40+ goals (December 2025 – January 2026).
