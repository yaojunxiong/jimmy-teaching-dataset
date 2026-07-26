# Jimmy Teaching Dataset Production Specification
Version: 1.0
Status: Production Ready

---

# Objective

Build a complete AI Simulation Dataset for all 50 lessons of
Minna no Nihongo.

This repository is the single source of truth for Jimmy AIOS.

Every lesson MUST follow exactly the same structure.

Never invent a new format.

---

# Repository Structure

```
docs/

lessons/

lesson-01/
lesson-02/
...
lesson-50/

progress.json

roadmap.md
```

---

# Lesson Structure

Each lesson contains:

```
lesson-XX/

metadata.yaml

scene.yaml

dialogue.yaml

simulation.yaml

roleplay.yaml
```

No additional files unless approved.

---

# metadata.yaml

Contains only:

```yaml
lesson:

title_ja:

title_zh:

scene:

difficulty:

estimated_minutes:
```

---

# scene.yaml

Contains:

```yaml
sceneDescription:

location:

characters:

learningGoal:

conversationSummary:
```

---

# dialogue.yaml

Contains every sentence.

Example

```yaml
sentenceId: 1

speaker:

japanese:

kana:

translationZh:
```

Never change textbook wording.

---

# simulation.yaml

This is the core.

Each sentence contains:

```yaml
sentenceId:

hintLevels:

learnerStates:

feedbackPool:

nextAction:
```

---

# Hint Levels

Exactly six.

Level 1

Scene reminder

Level 2

Chinese meaning

Level 3

Keywords

Level 4

Audio

Level 5

Opening words

Level 6

Full answer

Never change order.

---

# learnerStates

Exactly five.

```yaml
fluent

partial

weak

blank

off_topic_playful
```

No new states.

---

# feedbackPool

Every learnerState contains:

Positive feedback

Correction

Encouragement

Transition

No repeated wording.

---

# Roleplay

Contains:

Teacher

Student

Target sentence

Expected reply

Completion condition

---

# Progress

After every lesson update

progress.json

Example

```json
{
  "completedLessons": 7
}
```

---

# Commit Rules

One lesson

One commit

Commit message:

feat(lesson03): complete simulation dataset

Never combine multiple lessons.

---

# Quality Checklist

Before commit:

✅ Dialogue complete

✅ Hint complete

✅ learnerStates complete

✅ feedback complete

✅ YAML valid

✅ Importable by Jimmy AIOS

Only after all six pass

Commit.

---

# DO NOT

Do not rewrite textbook dialogue.

Do not simplify Japanese.

Do not invent grammar.

Do not change lesson order.

Do not skip hint levels.

Do not change learnerStates.

Do not change YAML schema.

---

# Long-term Goal

50 Lessons

↓

AI Simulation Dataset

↓

Jimmy AIOS

↓

Real User Feedback

↓

Dataset Evolution

↓

Jimmy Teaching Dataset v2
