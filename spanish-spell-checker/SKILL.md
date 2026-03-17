---
name: spanish-spell-checker
description: >
  Checks and corrects Spanish spelling, grammar, punctuation, and accented characters in any content. 
  Use this skill whenever Spanish text is being written, generated, or reviewed — including code comments, 
  documentation, commit messages, UI strings, emails, reports, or any output that contains Spanish words. 
  Trigger proactively when Claude produces Spanish content that may have missing accent marks (á, é, í, ó, ú, ü, ñ), 
  wrong punctuation (¿, ¡), incorrect gender agreement, wrong verb conjugations, or any other Spanish-specific 
  language issues. Also trigger when the user says "revisa el español", "corrige el texto", "check Spanish", 
  "fix accents", "grammar check in Spanish", or any similar request.
---

# Spanish Spelling & Grammar Checker Skill

## Purpose

Claude Code and other LLM tools are primarily trained on English and frequently make Spanish-specific errors:

- **Missing accent marks**: `esta` vs `está`, `el` vs `él`, `como` vs `cómo`, `que` vs `qué`
- **Missing special punctuation**: `¿` before questions, `¡` before exclamations
- **ñ substitution**: `año` written as `ano`, `niño` as `nino`
- **Gender agreement**: `el forma` → `la forma`, `una método` → `un método`
- **Verb conjugation errors**: tense or person mismatches
- **Capitalization**: Spanish doesn't capitalize days, months, languages, or nationalities
- **False friends**: words that look like English but mean something different

This skill ensures all Spanish content — regardless of where it appears — is linguistically correct.

---

## When to Apply This Skill

Apply whenever you produce or review **any Spanish-language content**, including:

- User-facing UI strings, labels, buttons, error messages
- Code comments written in Spanish
- Documentation, README files, or inline help text
- Commit messages, PR descriptions, or issue titles in Spanish
- Email drafts, Slack messages, or reports
- Test data, seed files, or sample content in Spanish
- Any multi-lingual file that mixes English and Spanish

> **Important**: Even when the primary task is not language-related (e.g., building a React component), if any Spanish strings are included, run this checker on them before finalizing the output.

---

## Checking Process

Follow these steps in order when checking Spanish content:

### Step 1 — Identify all Spanish text
Scan the full content. Mark every string, comment, label, or passage that is in Spanish or should be in Spanish.

### Step 2 — Apply the checklist below to each Spanish segment

#### 2a. Accent Marks (tildes)
Check every word against these high-frequency accent patterns:

| Correct | Common error | Rule |
|---|---|---|
| está, estás, está | esta, estas | Verb `estar` in present tense always carries accent |
| él (pronoun) | el (article) | Accent distinguishes pronoun from article |
| tú (pronoun) | tu (possessive) | Accent distinguishes pronoun from possessive |
| sí (yes / reflexive) | si (if) | Accent distinguishes affirmation from conditional |
| más (more) | mas (but/archaic) | Accent distinguishes quantity word |
| té (tea) | te (you/reflexive) | Accent on the noun |
| sé (I know / be!) | se (reflexive) | Accent on verb forms |
| mí (me, after prep) | mi (my) | Accent after prepositions |
| dé (give!) | de (of) | Accent on subjunctive/imperative |
| aún (still/yet) | aun (even) | Accent changes meaning |
| cómo, qué, cuándo, dónde, quién, cuál, cuánto | como, que, cuando, donde, quien, cual, cuanto | All interrogative/exclamative words require accent |
| también, además, así, después, aquí, allí, ahí, allá, acá | tambien, ademas, asi, despues, aqui, alli, ahi, alla, aca | Common adverbs frequently missing accents |

**Esdrújulas rule**: Words stressed on the antepenultimate (3rd-from-last) syllable ALWAYS take an accent:
- `número`, `código`, `módulo`, `página`, `índice`, `cálculo`, `método`, `técnico`, `público`, `único`, `básico`, `lógico`, `físico`, `química`, `médico`, `período`, `teléfono`, `artículo`, `capítulo`

**Verb tenses**: Future tense and imperfect always carry accents:
- Future: `será`, `tendrá`, `habrá`, `podrá`
- Imperfect: `había`, `tenía`, `podía`, `sabía`
- Present of estar (except `estoy`): `estás`, `está`, `estamos`, `están`

**-ción / -sión nouns always carry accent**: `acción`, `función`, `nación`, `revisión`, `solución`, `conexión`, `sesión`, `información`, `aplicación`, `configuración`

#### 2b. Special Punctuation
- Every question in Spanish must open with `¿` → `¿Cómo estás?`
- Every exclamation in Spanish must open with `¡` → `¡Bienvenido!`
- Check for inverted marks in UI tooltips, error messages, and help text

#### 2c. The letter ñ
Never substitute `n` for `ñ`. Critical words:
- `año` (not `ano` — this is a very vulgar error)
- `niño/niña`, `señor/señora/señorita`, `español`, `mañana`, `montaña`, `compañía`, `campaña`, `otoño`, `pequeño`, `tamaño`, `diseño`, `sueño`, `dueño`, `cariño`, `extraño`, `baño`, `paño`

#### 2d. Gender Agreement
Spanish nouns have grammatical gender. Articles, adjectives, and past participles must agree:
- `el/un` → masculine; `la/una` → feminine
- Words ending in `-ción`, `-sión`, `-dad`, `-tad`, `-tud`, `-umbre` → always feminine
- Words ending in `-ma` borrowed from Greek → often masculine (`el programa`, `el sistema`, `el problema`, `el tema`, `el idioma`, `el clima`)

#### 2e. Capitalization Rules
Spanish does NOT capitalize:
- Days of the week: `lunes`, `martes`, `miércoles`...
- Months: `enero`, `febrero`, `marzo`...
- Languages: `español`, `inglés`, `francés`
- Nationalities: `mexicano`, `panameño`, `colombiano`
- Titles before names: `el señor García`, `la doctora López`

#### 2f. Common Confusables
| Correct usage | Notes |
|---|---|
| `por qué` (why, in questions) | Two words, with accent |
| `porque` (because) | One word, no accent |
| `sino` (but rather) | vs `si no` (if not) |
| `haber` (auxiliary verb) | vs `a ver` (let's see) |

---

### Step 3 — Produce the corrected output

After identifying all issues:

1. **If checking existing content**: Return corrections in this format:
   ```
   ❌ [original text]
   ✅ [corrected text]
   📌 [brief rule explanation]
   ```

2. **If generating new content**: Produce the corrected content directly. Add a brief summary of corrections made, grouped by type.

3. **If corrections are numerous**: Group by category (tildes, puntuación, género, etc.).

---

### Step 4 — Edge Cases

- Flag ambiguous cases as `⚠️ ambiguous` and explain both possibilities
- Do NOT apply Spanish rules to code syntax, variable names, brand names, or proper nouns
- For code strings like `console.log("hola")` — check the string content, not the code
- Prefer Latin American Spanish: `ustedes` over `vosotros`; accept regional vocabulary

---

## Quick Reference: Most Frequent AI-Generated Spanish Errors

Always check these first:

**Missing esdrújula accents (very common in code/tech contexts):**
`codigo`→`código`, `pagina`→`página`, `numero`→`número`, `metodo`→`método`, `modulo`→`módulo`, `facil`→`fácil`, `util`→`útil`, `basico`→`básico`, `unico`→`único`, `logico`→`lógico`, `publico`→`público`, `tecnico`→`técnico`

**Missing accents on common words:**
`tambien`→`también`, `aqui`→`aquí`, `alli`→`allí`, `despues`→`después`, `ademas`→`además`, `asi`→`así`

**Missing accents on -ción/-sión nouns:**
`informacion`→`información`, `accion`→`acción`, `funcion`→`función`, `conexion`→`conexión`, `sesion`→`sesión`, `aplicacion`→`aplicación`

**Missing accents on interrogatives:**
`Como`→`Cómo`, `Que`→`Qué`, `Cuando`→`Cuándo`, `Donde`→`Dónde`

**Critical ñ errors:**
`nino`→`niño`, `espanol`→`español`, `manana`→`mañana`, `ano`→`año`

**Missing inverted punctuation:**
Start of question missing `¿`, start of exclamation missing `¡`

---

## Integration with Other Skills

When used alongside other skills (docx, pptx, xlsx, pdf), apply this checker **after** content generation as the final quality gate before producing the output file.

When reviewing code, check all string literals, comments, log messages, and documentation that contain Spanish.