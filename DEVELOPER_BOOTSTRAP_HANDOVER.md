# Developer Bootstrap Handover

This document provides complete bootstrap instructions for setting up a new development environment for the BC Native Plant & Pollinator Database (BC-NPPD).

## Prerequisites

- Python 3.11 or higher installed
- `python3` and `pip` commands available
- Git client available

## Repository Information

- **Organization**: UBC-FRESH
- **Repository**: bc-npp-database
- **Main branch**: main
- **Current development status**: Active development (Phase 0+)

## Bootstrap Steps

### 1. Clone the Repository

```bash
git clone https://github.com/UBC-FRESH/bc-npp-database.git
cd bc-npp-database
```

### 2. Create and Activate Virtual Environment

**Linux/macOS:**
```bash
python3 -m venv .venv
source .venv/bin/activate
```

**Windows PowerShell:**
```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

### 3. Install Development Dependencies

```bash
pip install --upgrade pip
pip install -e .[dev]
```

### 4. Install Workflow Dependencies (Optional but Recommended)

For running FreshForge workflows and provider scraping:

```bash
pip install -e .[workflow]
```

This installs:
- `freshforge` for workflow orchestration

## Initial Verification

Run the following commands to verify the setup:

```bash
# Check Python version
python --version  # Should be 3.11+

# Verify bc-nppd CLI is available
bc-nppd --help

# Run quality checks
ruff check .
pytest
```

### Build and Test Documentation (Optional)

```bash
cd docs
make html
sphinx-build -b html docs _build/html -W
```

## Project Structure Overview

```
bc-npp-database/
├── AGENTS.md                    # Agent working contract
├── CHANGE_LOG.md               # Project narrative
├── README.md                   # Public overview
├── ROADMAP.md                  # Phase/task roadmap
├── pyproject.toml              # Package metadata & dependencies
├── src/bc_npp_database/        # Main source code
│   ├── cli.py                  # CLI commands (see below)
│   └── ...
├── tests/                      # Test suite
├── docs/                       # Sphinx documentation
├── examples/                   # Example workflows and data
│   ├── provider_workflows/
│   └── workflows/
├── schemas/                    # Data schema and lookup files
├── data/                       # Data artifacts
│   ├── poc/vancouver/          # Vancouver PoC database
│   └── workbooks/              # Legacy workbook snapshots
├── local/                      # Ignored: local extraction area (gitignored)
└── outputs/                    # Ignored: generated outputs (gitignored)
```

## Key CLI Commands

### Development Commands

```bash
# Show all available commands
bc-nppd --help

# Validate source policy compliance
bc-nppd validate-source-policy <path>

# Generate Vancouver PoC list from workbook
bc-nppd generate-vancouver-poc-list <workbook_path> --out-dir data/poc/vancouver

# Generate evidence hardening artifacts
bc-nppd harden-vancouver-evidence --poc-dir data/poc/vancouver --out-dir data/poc/vancouver/evidence_hardening

# Generate usability layer artifacts
bc-nppd generate-vancouver-usability data/poc/vancouver/evidence_hardening --out-dir data/poc/vancouver/usability_layer

# Export usability data to Excel
bc-nppd export-usability-to-excel --output-file data/poc/vancouver/usability_export.xlsx

# Generate provider workflow template (for freshforge)
bc-nppd generate-provider-source-workflow PROV-SATIN --out-path examples/provider_workflows/satinflower_source_review.yaml
```

### Provider Data Workflow

For integrating new species from provider sources:

1. **Generate and run FreshForge workflow:**
   ```bash
   # Create workflow template for a provider
   bc-nppd generate-provider-source-workflow PROV-SATIN --out-path examples/provider_workflows/satinflower_source_review.yaml
   
   # Run the workflow with FreshForge
   freshforge run examples/provider_workflows/satinflower_source_review.yaml
   ```

2. **Review sandbox data:**
   ```bash
   bc-nppd build-provider-approval-review inputs/ --out-dir outputs/
   open outputs/provider_approval_review/index.html  # View in browser
   ```

3. **Apply approved provider data to database (MERGES with existing):**
   ```bash
   bc-nppd apply-provider-approval-sequence \
     outputs/provider_approval_review/PROV-SATIN/approval_manifest.csv \
     --poc-dir data/poc/vancouver_backup \
     --out-dir data/poc/vancouver
   ```

**Important:** Use `apply-provider-approval-sequence` (not `apply-provider-approvals`) to MERGE new provider data with existing database content.

## FreshForge Provider Workflows

The repository includes pre-configured FreshForge workflow templates for four default providers:

- `examples/workflows/providers/PROV-SATIN.yaml` - Satinflower
- `examples/workflows/providers/PROV-NWM.yaml` - Northwest Meadowscapes
- `examples/workflows/providers/PROV-WCS.yaml` - West Coast Seeds
- `examples/workflows/providers/PROV-PREMIER.yaml` - Premier Pacific Seeds

To use these:

1. Copy the appropriate template file to your working directory
2. Run with FreshForge: `freshforge run <workflow_file>.yaml`
3. Review outputs in `outputs/provider_sandbox_source_sweep/PROV-XXX/`

## GitIGNOREd Directories (Do NOT commit)

The following directories are gitignored and should not be committed:

- `tmp/` - Notes, experiments, source bundles
- `local/` - Local extraction area for seed archives and provider data
- `outputs/` - Generated outputs, approval manifests, review apps
- `data/raw/` - Raw data files
- `data/private/` - Private project data
- `.venv/` or `venv/` - Python virtual environment

## Data Files That May Exist (Not in Repo)

After bootstrapping, you may need to restore or regenerate:

1. **Vancouver PoC Database** (`data/poc/vancouver/`)
   - Generated from workbook normalization
   - Contains ~54 plant records initially
   
2. **Evidence Hardening Artifacts**
   ```bash
   bc-nppd generate-vancouver-usability data/poc/vancouver/evidence_hardening --out-dir data/poc/vancouver/usability_layer
   ```

3. **Provider Approval Manifests** (`outputs/provider_approval_review/`)
   - Generated from FreshForge workflow outputs
   
4. **Provider Sandbox Data** (`outputs/provider_sandbox_source_sweep/`)
   - Generated by provider scraping workflows

## Troubleshooting

### ImportError: No module named 'bc_npp_database'
```bash
pip install -e .
```

### FreshForge command not found
```bash
pip install -e .[workflow]
# Or directly: pip install freshforge @ git+https://github.com/UBC-FRESH/freshforge.git@v0.1.0a2
```

### Permission denied when running workflows
Ensure your user has write permissions to:
- `examples/workflows/providers/`
- `outputs/`
- `local/`

## Verification Checklist

After setup, verify all of these work:

- [ ] Virtual environment activates correctly
- [ ] `bc-nppd --help` shows available commands
- [ ] `bc-nppd info` prints project information
- [ ] Ruff passes: `ruff check .`
- [ ] Tests pass: `pytest`
- [ ] Documentation builds: `sphinx-build -b html docs _build/html -W`

## Next Steps After Bootstrap

1. Review `ROADMAP.md` for current development phases
2. Check `CHANGE_LOG.md` for recent project history
3. Explore `src/bc_npp_database/cli.py` to understand available commands
4. Read `docs/` directory for domain-specific documentation
5. Review existing FreshForge workflows in `examples/workflows/`
6. Start development on the next roadmap phase

## Support Resources

- **Project Homepage**: https://github.com/UBC-FRESH/bc-npp-database
- **Issue Tracker**: https://github.com/UBC-FRESH/bc-npp-database/issues
- **Changelog**: https://github.com/UBC-FRESH/bc-npp-database/blob/main/CHANGE_LOG.md

## Environment Notes

This repository uses:
- Python 3.11+ (tested on 3.11 and 3.12)
- setuptools for packaging
- Typer for CLI
- FreshForge for workflow orchestration
- Sphinx for documentation
- Ruff and pytest for code quality
