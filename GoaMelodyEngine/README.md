# Goa Melody Engine (VST3 / AU)

מחולל MIDI שיושב בתוך ה־DAW: מוטיב קודם, תווים אחר כך. 4 מצבים (Melody, Acid, Arp, Chords → melody), 34 סולמות + Custom, 8 סגנונות, Generate → Keep → Mutate, Melody DNA מקובץ MIDI, וסינת' Preview פנימי.

הפלאגין מתנגן מסונכרן לשעון ה־DAW, מוציא MIDI החוצה לסינת' שלך, ואפשר לגרור ממנו קליפ MIDI ישר לטראק.

---

## דרך 1: בנייה אוטומטית ב־GitHub (בלי להתקין כלום)

1. פותחים ריפו חדש ב־GitHub ומעלים אליו את כל התיקייה הזו (כולל התיקייה `.github`).
2. בלשונית **Actions** הבנייה רצה לבד (Windows ו־macOS). לוקח בערך 10–15 דקות.
3. בסיום, בתחתית עמוד הריצה, תחת **Artifacts**: מורידים `GoaMelodyEngine-Windows` או `GoaMelodyEngine-macOS`.

## דרך 2: בנייה מקומית

צריך CMake 3.22+ ו־Visual Studio 2022 (Windows) או Xcode (Mac). JUCE יורד אוטומטית.

```
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release
```

התוצרים: `build/GoaMelodyEngine_artefacts/Release/VST3` (ו־`AU`, `Standalone` ב־Mac).

בדיקת המנוע בלבד (בלי JUCE):
```
g++ -std=c++17 -O2 tests/engine_test.cpp Source/Engine.cpp -o engine_test && ./engine_test
```

---

## התקנה

| מערכת | לאן להעתיק |
|---|---|
| Windows VST3 | `C:\Program Files\Common Files\VST3\` |
| macOS VST3 | `~/Library/Audio/Plug-Ins/VST3/` |
| macOS AU | `~/Library/Audio/Plug-Ins/Components/` |

ב־Mac, הפלאגין לא חתום אצל Apple, אז פעם אחת בטרמינל:
```
xattr -dr com.apple.quarantine ~/Library/Audio/Plug-Ins/VST3/"Goa Melody Engine.vst3"
xattr -dr com.apple.quarantine ~/Library/Audio/Plug-Ins/Components/"Goa Melody Engine.component"
```

---

## שימוש ב־Ableton Live

Ableton לא טוען VST3 של צד שלישי כ־MIDI Effect, לכן הפלאגין הוא Instrument שמוציא MIDI:

1. טראק MIDI 1: טוענים **Goa Melody Engine**. לוחצים Play ב־Live והוא מתנגן בסנכרון, עם צליל ה־Preview הפנימי.
2. טראק MIDI 2: טוענים Serum / Vital / 303.
   - **MIDI From** → טראק 1, ובתפריט השני → **Goa Melody Engine**.
   - **Monitor** → **In**.
3. בפלאגין מכבים **Preview sound** כדי לשמוע רק את הסינת' שלך.

או פשוט גוררים את **Drag MIDI into your DAW** לטראק, ומקבלים קליפ רגיל.

**FL Studio:** בהגדרות ה־Wrapper של הפלאגין קובעים **MIDI output port** (למשל 1), ובסינת' את אותו מספר כ־**MIDI input port**.
**Bitwig / Reaper / Cubase / Studio One:** ה־MIDI יוצא מהפלאגין ישירות לפי הניתוב הרגיל של התוכנה.
**Logic:** Logic לא מנתב MIDI שיוצא מ־Instrument. בינתיים: גרירת קליפ, או גרסת AU MIDI FX בהמשך.

---

## מבנה הקוד

- `Source/Engine.*`: כל ההיגיון המוזיקלי. C++17 טהור, בלי תלות ב־JUCE.
- `Source/PluginProcessor.*`: סנכרון לשעון ה־DAW, תזמון נוטים ברמת הדגימה, slides כנוטים חופפים (בסגנון 303), סינת' Preview, שמירת מצב בפרויקט.
- `Source/PluginEditor.*`: הממשק.
- `tests/engine_test.cpp`: בדיקת עומס של המנוע על כל הסולמות, המצבים והסגנונות.

כל 8 המלודיות, ה־Keep וההגדרות נשמרים בתוך פרויקט ה־DAW.
