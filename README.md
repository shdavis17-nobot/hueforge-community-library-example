# HueForge Community Library Template

A template repository for publishers creating community filament libraries for HueForge 3D lithophane software.

## Quick Start

1. **Clone or fork** this template
2. **Add your filaments** as JSON files to `libraries/`
3. **Configure the hook** (one-time setup)
4. **Push to GitHub** and register the manifest URL in HueForge

## Repository Structure

```
hueforge-community-library-template/
├── libraries/                    # Your filament library JSON files go here
│   ├── .gitkeep
│   ├── my-pla-filaments.json
│   └── specialty-colors.json
├── .githooks/
│   └── pre-commit               # Auto-generates manifest.json on commit
├── generate_manifest.py         # Standalone manifest generation script
├── manifest.json                # Generated library index (don't edit manually)
└── README.md
```

## Adding Filaments

1. Create a `.json` filament library file in `libraries/` following HueForge's filament format
2. The `generate_manifest.py` script will automatically discover and index it

**Filament JSON format** — each file should contain:

```json
{
  "Filaments": [
    {
      "name": "PLA White",
      "manufacturerName": "Your Vendor",
      "materialType": "PLA",
      "colorLabel": "White",
      "colorHex": "#FFFFFF",
      "defaultTemp": 200,
      ...
    }
  ]
}
```

Refer to the [HueForge filament specification](https://github.com/HueForge/hueforge/blob/main/docs/filament-format.md) for the complete schema.

## One-Time Setup

After cloning this repository, enable the auto-commit hook:

```bash
git config core.hooksPath .githooks
```

This enables automatic manifest regeneration whenever you commit changes to `libraries/`.

## Configuration

Edit `.githooks/pre-commit` and set:

- **`SOURCE_NAME`**: The human-readable name for your collection (e.g., "Acme PLA Library")
- **`BASE_URL`**: The raw GitHub URL to your `libraries/` folder (e.g., `https://raw.githubusercontent.com/your-username/your-repo/main/libraries`)

**Do not include "HueForge" in `SOURCE_NAME`** — the community library system automatically prepends "[Unofficial]" to source names that don't contain it.

### Example Configuration

```bash
# In .githooks/pre-commit:
SOURCE_NAME="Acme Premium PLA Collection"
BASE_URL="https://raw.githubusercontent.com/acme/hueforge-filaments/main/libraries"
```

## Generating the Manifest Manually

If you prefer not to use the git hook, or for testing:

```bash
python generate_manifest.py \
  --source-name "My PLA Collection" \
  --base-url https://raw.githubusercontent.com/your-username/your-repo/main/libraries
```

This scans `libraries/`, counts filaments in each `.json` file, computes SHA256 checksums, and writes `manifest.json`.

## Registering in HueForge

1. In HueForge, open **Filaments → Manage Community Sources**
2. Click **+ Add Source**
3. Paste the raw manifest URL:
   ```
   https://raw.githubusercontent.com/your-username/your-repo/main/manifest.json
   ```
4. Click **OK** — HueForge will fetch and verify the manifest

Your filaments now appear in the **Filaments** menu under **Community** with a badge showing your source.

## Publishing Updates

1. Add or modify `.json` files in `libraries/`
2. Commit and push to GitHub
   ```bash
   git add libraries/
   git commit -m "Add acme-pla-v2 library"
   git push origin main
   ```
3. The pre-commit hook automatically regenerates `manifest.json`
4. HueForge users see updates when they refresh **Manage Community Sources**

## Manifest Schema

`manifest.json` is auto-generated and looks like:

```json
{
  "source_name": "My PLA Collection",
  "libraries": [
    {
      "name": "my-pla-filaments",
      "filename": "my-pla-filaments.json",
      "category": "community",
      "url": "https://raw.githubusercontent.com/you/repo/main/libraries/my-pla-filaments.json",
      "sha256": "a1b2c3d4...",
      "filament_count": 42
    }
  ]
}
```

**Fields:**
- `source_name`: Display name for your collection (from config)
- `name`: Stem of the filename (used as library ID)
- `category`: Always `"community"` for user-contributed libraries
- `url`: Full download URL (auto-constructed from `--base-url`)
- `sha256`: Integrity check over normalized line endings
- `filament_count`: Number of filaments in that library file

## Troubleshooting

**"Pre-commit hook failed"**
- Ensure Python 3 is installed and in PATH
- Check that `generate_manifest.py` has execute permissions
- Verify `--source-name` and `--base-url` are set in `.githooks/pre-commit`

**"No .json files in libraries/"**
- The hook requires at least one `.json` file in `libraries/` to generate a manifest
- If starting fresh, add a minimal test file temporarily

**"SHA256 mismatch" in HueForge**
- Ensure line endings in JSON files are consistent (CRLF vs LF)
- The script normalizes to LF before hashing; if you edit files on mixed systems, recommend `.gitattributes`:
  ```
  *.json text eol=lf
  ```

## License

This template is provided under the same license as HueForge (see the main HueForge repository).

## Support

For questions about filament format, integrating with HueForge, or community library architecture, refer to the [HueForge community documentation](https://github.com/HueForge/hueforge) or open an issue in the main repository.
