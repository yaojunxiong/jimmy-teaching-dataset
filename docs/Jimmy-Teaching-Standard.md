# Jimmy Teaching Standard

Version: 1.0.0  
Status: Production Ready  
Scope: Minna no Nihongo Lessons 01–50 conversation simulation dataset V1

## 1. Purpose

This repository is the single source of truth for Jimmy AIOS conversation simulation data.

The V1 goal is not to create a complete teaching course. The V1 goal is to transform the existing verified textbook conversation data into a stable dataset that lets Jimmy AIOS:

1. present the conversation scene;
2. prompt the learner sentence by sentence;
3. classify the learner response;
4. provide progressive hints;
5. choose the next teaching action;
6. complete the full conversation without immediately revealing answers.

The textbook source data must be reused. Do not rewrite or paraphrase textbook dialogue.

## 2. Source of truth

Primary local source:

`/Users/jimmy/TypingJapaneseWords-022b/next-app/src/data/minna/`

Preferred source files:

- `recitation/lesson-XX.json` for ordered conversation lines, speaker, Japanese and Chinese;
- `lessons/lesson-XX.json` for lesson metadata and available scene information;
- existing audio/image references when present.

Source priority:

1. existing verified lesson data;
2. existing recitation data;
3. existing conversation metadata;
4. `null` when a non-required field is unavailable.

Never invent textbook sentences, translations, speakers, audio paths or image paths.

## 3. Target repository structure

Each lesson must contain exactly one V1 dataset file:

```text
lessons/
  lesson-01/
    simulation.yaml
  lesson-02/
    simulation.yaml
  ...
  lesson-50/
    simulation.yaml
```

Project-level files:

```text
README.md
roadmap.md
progress.json
docs/Jimmy-Teaching-Standard.md
```

Do not add multiple lesson files in V1 unless this standard is revised first.

## 4. Canonical lesson schema

Every `simulation.yaml` must follow this top-level structure and field order:

```yaml
schemaVersion: "1.0.0"
lesson: 1
lessonId: "lesson-01"
title:
  ja: null
  zh: null
source:
  repository: "TypingJapaneseWords-022b"
  recitationFile: "next-app/src/data/minna/recitation/lesson-01.json"
  lessonFile: "next-app/src/data/minna/lessons/lesson-01.json"
  generatedFromVerifiedSource: true
scene:
  summaryZh: null
  image: null
characters: []
nodes: []
learnerStates: {}
redirectPolicy: {}
observationSchema: {}
quality:
  sourceDialoguePreserved: true
  nodeCount: 0
  validationStatus: "passed"
```

Required top-level keys:

- `schemaVersion`
- `lesson`
- `lessonId`
- `title`
- `source`
- `scene`
- `characters`
- `nodes`
- `learnerStates`
- `redirectPolicy`
- `observationSchema`
- `quality`

No required top-level key may be omitted.

## 5. Lesson identifiers

Use:

```yaml
lesson: 1
lessonId: "lesson-01"
```

Rules:

- `lesson` is an integer from 1 to 50;
- `lessonId` uses two digits;
- folder name and `lessonId` must match;
- source paths use the same two-digit lesson number.

## 6. Title

```yaml
title:
  ja: null
  zh: null
```

Use an existing verified title when available. Otherwise use `null`.

Do not generate a creative title.

## 7. Scene

```yaml
scene:
  summaryZh: null
  image: null
```

Rules:

- use an existing verified Chinese scene summary when available;
- use an existing image path when available;
- otherwise use `null`;
- do not infer detailed events not explicitly supported by source data.

## 8. Characters

`characters` is a deduplicated ordered list derived from dialogue speakers.

Example:

```yaml
characters:
  - id: "speaker-01"
    name: "ミラー"
  - id: "speaker-02"
    name: "佐藤"
```

Rules:

- preserve the source speaker name exactly;
- create IDs in first-appearance order;
- do not invent personal information, gender, job or relationship;
- system or narration speakers are allowed if present in source data.

## 9. Nodes

Each source conversation line becomes exactly one node.

Node order must match source order exactly.

Canonical node schema:

```yaml
nodes:
  - nodeId: "lesson-01-node-01"
    order: 1
    speakerId: "speaker-01"
    speaker: "ミラー"
    targetText: "はじめまして。"
    translationZh: "初次见面。"
    kana: null
    audio: null
    image: null
    hints:
      scene: "第一次见面时要说的固定表达。"
      zh: "初次见面。"
      keywords:
        - "はじめまして"
      audio: null
      opening: "はじめ…"
      answer: "はじめまして。"
    completion:
      isFinalNode: false
      nextNodeId: "lesson-01-node-02"
```

Required node keys:

- `nodeId`
- `order`
- `speakerId`
- `speaker`
- `targetText`
- `translationZh`
- `kana`
- `audio`
- `image`
- `hints`
- `completion`

### 9.1 Dialogue preservation

`targetText`, `translationZh` and `speaker` must be copied from verified source data.

Do not:

- rewrite punctuation for style;
- simplify Japanese;
- modernize expressions;
- merge lines;
- split lines;
- add missing dialogue from memory.

### 9.2 Optional fields

Use existing values for `kana`, `audio` and `image` when verified source data provides them.

Otherwise use `null`.

Do not calculate kana automatically in V1.

## 10. Six progressive hints

Every node must contain exactly these six hint keys in this order:

```yaml
hints:
  scene: "..."
  zh: "..."
  keywords: []
  audio: null
  opening: "..."
  answer: "..."
```

The teaching sequence is:

1. `scene`: remind the learner of the immediate conversational situation;
2. `zh`: show the Chinese meaning;
3. `keywords`: show one to three essential Japanese words already present in the target sentence;
4. `audio`: provide the verified source audio path or `null`;
5. `opening`: reveal only the shortest useful beginning of the target sentence;
6. `answer`: reveal the full exact textbook sentence.

Rules:

- never change the order;
- `answer` must equal `targetText` exactly;
- `keywords` must be copied from words occurring in `targetText`;
- do not include the full answer in levels 1–5;
- `opening` should normally be the first meaningful word or phrase, not more than roughly one third of the sentence;
- scene hints may be concise generated teaching text, but must not contradict the source scene;
- Chinese hint should normally equal `translationZh`.

## 11. Completion routing

For each non-final node:

```yaml
completion:
  isFinalNode: false
  nextNodeId: "lesson-01-node-02"
```

For the final node:

```yaml
completion:
  isFinalNode: true
  nextNodeId: null
```

Node IDs must be continuous with no gaps.

## 12. Learner states

Every lesson must define exactly these five shared learner states:

```yaml
learnerStates:
  fluent:
    teachingAction: "affirm_and_advance"
    emotionGoal: "confidence"
    feedbackPool: []
    nextAction: "next_node"
  partial:
    teachingAction: "confirm_correct_part_and_prompt"
    emotionGoal: "supported_recall"
    feedbackPool: []
    nextAction: "next_hint"
  weak:
    teachingAction: "reduce_difficulty"
    emotionGoal: "low_pressure"
    feedbackPool: []
    nextAction: "next_hint"
  blank:
    teachingAction: "start_progressive_hinting"
    emotionGoal: "safe_restart"
    feedbackPool: []
    nextAction: "next_hint"
  off_topic_playful:
    teachingAction: "warm_redirect_to_conversation"
    emotionGoal: "connection_without_drift"
    feedbackPool: []
    nextAction: "repeat_current_node"
```

No additional learner states are allowed in V1.

### 12.1 Feedback pools

Each learner state must contain at least three short Chinese feedback messages.

Feedback requirements:

- supportive and natural;
- concise enough for conversational UI;
- no grammar lecture;
- no shame, punishment or negative scoring language;
- no full target answer before hint level 6;
- conversation content remains the central topic;
- messages within the same pool should not be identical.

Recommended intent:

- `fluent`: confirm success and advance;
- `partial`: recognize the remembered part and offer the next hint;
- `weak`: reduce pressure and give a stronger hint;
- `blank`: reassure and begin from scene or Chinese meaning;
- `off_topic_playful`: acknowledge briefly, then return to the current textbook scene.

## 13. Redirect policy

Every lesson must include:

```yaml
redirectPolicy:
  conversationFirst: true
  allowBriefSocialResponse: true
  maxOffTopicTurns: 1
  redirectTarget: "current_node"
  revealAnswerOnlyAtHintLevel: 6
```

Meaning:

- Jimmy may respond warmly to the learner;
- chatting must remain anchored to the current textbook conversation;
- after one off-topic turn, Jimmy returns to the current line;
- Jimmy does not immediately provide the answer;
- the full answer is shown only at hint level 6 or after an explicit learner request to reveal it.

## 14. Observation schema

Every lesson must include:

```yaml
observationSchema:
  recordPerAttempt:
    - "lessonId"
    - "nodeId"
    - "timestamp"
    - "learnerState"
    - "hintLevelUsed"
    - "responseText"
    - "completed"
  aggregateMetrics:
    - "attemptCount"
    - "completionRate"
    - "averageHintLevel"
    - "mostDifficultNodeIds"
```

This section defines observation fields only. Do not insert fabricated learner observations into lesson datasets.

## 15. Quality block

Each lesson must end with:

```yaml
quality:
  sourceDialoguePreserved: true
  nodeCount: 4
  validationStatus: "passed"
```

Rules:

- `nodeCount` must equal the source recitation line count;
- `sourceDialoguePreserved` may be `true` only after exact source comparison;
- `validationStatus` is `passed` only after all checks pass;
- do not commit a lesson with `validationStatus: failed`.

## 16. Validation requirements

Before committing a lesson, validate all of the following:

1. YAML parses successfully.
2. Folder and lesson identifiers match.
3. Source files exist.
4. Node count equals source line count.
5. Node order is continuous.
6. Speaker, Japanese and Chinese match source data exactly.
7. Every node contains six hint keys.
8. Every answer hint exactly equals target text.
9. Every non-final node points to the next node.
10. Final node has `nextNodeId: null`.
11. Exactly five learner states exist.
12. Every learner state has at least three feedback messages.
13. Required redirect policy fields exist.
14. Required observation fields exist.
15. No unverified textbook content was invented.

If any validation fails, stop before commit and report the exact lesson and field.

## 17. Generation process

Production order:

```text
Lesson01 → Lesson02 → ... → Lesson50
```

For each lesson:

1. read verified local source data;
2. generate `lessons/lesson-XX/simulation.yaml`;
3. validate against this standard;
4. update `progress.json`;
5. commit;
6. push to `main`;
7. continue automatically to the next lesson.

No manual confirmation is needed between successful lessons.

Stop only when:

- all 50 lessons are complete;
- verified source data is missing;
- source files conflict;
- this standard is internally contradictory;
- validation cannot pass without inventing textbook content.

## 18. Commit rules

One lesson equals one commit.

Commit format:

```text
feat(lesson01): complete conversation simulation dataset
```

Use the corresponding two-digit lesson number.

Each lesson commit must include only:

- that lesson's new `simulation.yaml`;
- the corresponding `progress.json` update.

The standard itself is changed only in a dedicated documentation commit.

## 19. progress.json

Required project progress structure:

```json
{
  "version": "1.0.0",
  "datasetType": "conversation-simulation-v1",
  "completedLessons": 0,
  "targetLessons": 50,
  "currentLesson": 1,
  "status": "Production",
  "lessons": {
    "lesson01": "pending",
    "lesson02": "pending"
  }
}
```

The `lessons` object must contain lesson01 through lesson50.

Allowed lesson status values:

- `pending`
- `in_progress`
- `completed`
- `blocked`

After each successful lesson commit:

- mark that lesson `completed`;
- increment `completedLessons`;
- set `currentLesson` to the next incomplete lesson;
- set project `status` to `Completed` after Lesson50.

## 20. V1 exclusions

Do not add these to V1 unless separately authorized:

- grammar lectures;
- full vocabulary lessons;
- quizzes unrelated to conversation recall;
- generated replacement dialogue;
- free-form general chat datasets;
- learner profiles;
- actual observation records;
- model-specific prompts;
- UI code;
- deployment code;
- database migrations.

## 21. Core teaching principle

Jimmy AIOS follows this recall chain:

```text
conversation image or scene
→ recall the Chinese meaning
→ recall key Japanese words
→ organize the complete Japanese line
→ compare with the exact textbook sentence
```

Jimmy does not give the answer immediately. Jimmy identifies where the learner is stuck and provides the smallest useful next hint.

Conversation content is always the main axis. Brief social interaction is allowed only as a teaching method for returning to and deepening the current lesson conversation.

## 22. Governance

Code follows Dataset. Dataset does not degrade to accommodate current code limitations.

If Jimmy AIOS cannot consume a valid dataset defined by this standard, update the engine rather than weakening verified textbook content or teaching logic.

Any schema change requires:

1. a dedicated update to this document;
2. a schema version increment;
3. an explanation of migration impact;
4. no silent modification by a production agent.
