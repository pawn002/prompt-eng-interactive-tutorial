# Prompt Engineering Tutorial - Progress Log

## Setup Completed (2026-01-26)

### Environment Information
- **Python Version**: 3.11.4
- **Pip Version**: 25.3 (updated from 23.1.2)
- **Platform**: Windows
- **Working Directory**: `C:\Users\pawn0\_dev\prompt-eng-interactive-tutorial`

### Installation Steps Taken

#### 1. Updated Pip
```bash
python -m pip install --upgrade pip --user
```

#### 2. Installed Jupyter Notebook
```bash
pip install --user notebook anthropic
```

**Packages Installed:**
- notebook 7.5.3
- anthropic 0.76.0
- Plus all dependencies (jupyter-server, jupyterlab, ipykernel, etc.)

#### 3. Started Jupyter Notebook
```bash
cd "Anthropic 1P"
python -m notebook
```

**Access URL:** http://localhost:8888/tree?token=a3537be27915319e3e9aa79be2f33491ca4031b8301fb9f5

### Configuration Steps

1. **Opened Notebook**: `00_Tutorial_How-To.ipynb`
2. **Configured API Key Securely**:
   - Created `.env` file in `Anthropic 1P/` directory
   - Added `ANTHROPIC_API_KEY` and `MODEL_NAME` to `.env`
   - Installed `python-dotenv` package
   - Updated notebook to load credentials from `.env` file
3. **Set Model**: Using `MODEL_NAME = "claude-3-haiku-20240307"`
4. **Added Credits**: Purchased API credits at https://console.anthropic.com/
5. **Tested API**: Successfully ran test prompt "Hello, Claude!"

### How to Run the Notebooks (Quick Reference)

#### Starting Jupyter
1. Open terminal/command prompt
2. Navigate to the tutorial directory:
   ```bash
   cd "C:\Users\pawn0\_dev\prompt-eng-interactive-tutorial"
   ```
3. Enter the Anthropic folder:
   ```bash
   cd "Anthropic 1P"
   ```
4. Start Jupyter:
   ```bash
   python -m notebook
   ```
5. Open the URL that appears in your browser (includes token)

#### Using Notebooks
- **Run a cell**: Click inside it and press `Shift + Enter`
- **Save notebook**: Press `Ctrl + S`
- **Stop Jupyter**: Press `Ctrl + C` twice in the terminal

#### Special Jupyter Syntax
- `!pip install package` - Runs shell commands from within notebook
- `%store variable` - Stores variables across notebooks

#### API Key Configuration
The notebook now loads API credentials from `.env` file:
```bash
# Edit Anthropic 1P/.env and add your API key
ANTHROPIC_API_KEY=sk-ant-api03-...
MODEL_NAME=claude-3-haiku-20240307
```
The `.env` file is protected by `.gitignore` and won't be committed.

### Tutorial Structure

**Beginner (Chapters 1-3):**
- [x] Chapter 1: Basic Prompt Structure ✅ Completed (2026-01-27)
- [x] Chapter 2: Being Clear and Direct ✅ Completed (2026-01-27)
- [ ] Chapter 3: Assigning Roles (🔄 In Progress - Exercises)

**Intermediate (Chapters 4-7):**
- [ ] Chapter 4: Separating Data from Instructions
- [ ] Chapter 5: Formatting Output & Speaking for Claude
- [ ] Chapter 6: Precognition (Thinking Step by Step)
- [ ] Chapter 7: Using Examples

**Advanced (Chapters 8-9):**
- [ ] Chapter 8: Avoiding Hallucinations
- [ ] Chapter 9: Building Complex Prompts (Industry Use Cases)

**Appendix:**
- [ ] Chaining Prompts
- [ ] Tool Use
- [ ] Search & Retrieval

### Resources
- **Tutorial Location**: `C:\Users\pawn0\_dev\prompt-eng-interactive-tutorial\Anthropic 1P\`
- **API Console**: https://console.anthropic.com/
- **Answer Key**: https://docs.google.com/spreadsheets/d/1jIxjzUWG-6xBVIa2ay6yDpLyeuOh_hR_ZB75a47KX_E/edit?usp=sharing
- **Google Sheets Version**: https://docs.google.com/spreadsheets/d/19jzLgRruG9kjUQNKtCg1ZjdD6l6weA6qRXG5zLIAhC8/edit?usp=sharing
- **Hints File**: `hints.py` (contains hints for all exercises)

### Issues Encountered & Resolved

**Issue 1: Installation Permission Warnings**
- Windows showed permission warnings during pip install
- **Resolution**: Used `--user` flag: `pip install --user notebook anthropic`

**Issue 2: Jupyter Command Not Found**
- `jupyter` command not in PATH
- **Resolution**: Used `python -m notebook` instead

**Issue 3: API Credit Balance Error**
```
BadRequestError: Your credit balance is too low to access the Anthropic API
```
- **Resolution**: Added credits via Plans & Billing at console.anthropic.com

### Security Configuration

**Protected Files Added to `.gitignore`:**
- `.env` and `*/.env` - Environment files with API keys
- `secrets.py` and `*/secrets.py` - Python secrets files
- `.claude/` - Claude Code configuration
- `.ipynb_checkpoints/` - Jupyter checkpoint files

**Files Created:**
- `CLAUDE.md` - Repository documentation for Claude Code
- `PROGRESS_LOG.md` - This file (learning progress tracker)
- `Anthropic 1P/.env` - Secure API key storage (not committed)

### Current Status
✅ Environment fully set up
✅ Jupyter Notebook running
✅ API key configured securely via .env file
✅ Git repository configured for personal learning
✅ Ready to start Chapter 1

---

## Learning Progress

### Session 1 (2026-01-26)
- Completed setup and configuration
- Tested API connection with "Hello, Claude!" prompt
- Configured secure API key storage using `.env` file
- Set up git repository for personal learning with proper .gitignore
- Created documentation (CLAUDE.md, PROGRESS_LOG.md)

**Git Commits Made:**
- `ef2cef0` - Add documentation for personal learning environment
- `3472fef` - Update gitignore for personal learning environment
- `1f2c0a6` - Add .env and secrets.py to gitignore
- `f3191a4` - Configure notebook to use .env file for API key

**Next**: Begin Chapter 1: Basic Prompt Structure

### Session 2 (2026-01-27)
- ✅ Completed Chapter 1: Basic Prompt Structure
- ✅ Completed Chapter 2: Being Clear and Direct
- Started Chapter 3: Assigning Roles
- Finished all exercises in `01_Basic_Prompt_Structure.ipynb`
- Finished all exercises in `02_Being_Clear_and_Direct.ipynb`
- Working through notebook: `03_Assigning_Roles_Role_Prompting.ipynb`
- Currently at: **Exercises** section

**Key Learnings from Chapter 1:**
- Human/Assistant message format
- Basic prompt structure with Claude
- How to interact with Claude's API using the Messages format

**Key Learnings from Chapter 2:**
- Importance of clear and direct instructions
- How specificity improves prompt performance
- Techniques for writing unambiguous prompts

**Current Focus:**
- Learning how to assign roles to Claude (role prompting)
- Understanding how roles affect Claude's responses
- Preparing to complete Chapter 3 exercises

**Next**: Complete Chapter 3 exercises and move to Chapter 4

---

## Notes & Learnings
*(Add your notes as you progress through the tutorial)*

