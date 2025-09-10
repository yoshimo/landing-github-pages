# Blokada Landing GitHub Pages - Scripts Documentation

## Overview

This repository contains scripts for managing blocklists and mirrors for the Blokada project. The scripts handle whitelist generation, blocklist processing, and mirror synchronization.

## Scripts Workflow

### Whitelist Management

1. **Manual Whitelist Entry**
   - Add domains to `scripts/whitelist-manual` (one domain per line)
   - This file contains manually curated domains that should never be blocked

2. **Whitelist Generation**
   - Run `./scripts/gen-whitelist.py` to generate the master whitelist
   - This script combines:
     - Manual whitelist (`scripts/whitelist-manual`)
     - External whitelist sources (`scripts/whitelist-sets`)
   - Output: `scripts/whitelist` (master whitelist file)

3. **Mirror Synchronization**
   - Run `./scripts/sync-mirrors.sh` to update all mirrors
   - This script calls:
     - `./ddg.py` - Processes DuckDuckGo tracker radar (respects whitelist)
     - `./exodus.py` - Processes Exodus Privacy trackers (respects whitelist)  
     - `./mirror.py` - Downloads external blocklists (respects whitelist)

## Key Scripts

### `gen-whitelist.py`
- **Purpose**: Generates master whitelist from manual entries and external sources
- **Input**: 
  - `whitelist-manual` - manually added domains
  - `whitelist-sets` - URLs to external whitelist sources
- **Output**: `whitelist` - combined whitelist file
- **Usage**: `./gen-whitelist.py [-o <output-file>]`

### `ddg.py`
- **Purpose**: Processes DuckDuckGo Tracker Radar data
- **Filtering**: Uses `scripts/whitelist` to filter out whitelisted domains
- **Output**: `../blocklists/ddgtrackerradar/standard/hosts.txt`
- **Features**: 
  - Filters by category (skips CDN, Online Payment, Non-Tracking)
  - Filters by subdomain patterns
  - Respects resource count thresholds

### `exodus.py`
- **Purpose**: Processes Exodus Privacy tracker database
- **Filtering**: Uses `scripts/whitelist` to filter out whitelisted domains
- **Output**: `../blocklists/exodusprivacy/standard/hosts.txt`
- **Features**: 
  - Filters out common subdomains (www, api, cdn)
  - Respects master whitelist

### `mirror.py`
- **Purpose**: Downloads and mirrors external blocklists
- **Filtering**: Uses `scripts/whitelist` to filter out whitelisted domains (added by Claude)
- **Output**: `../mirror/v5/` directory structure
- **Features**:
  - Downloads from multiple external sources (1hosts, StevenBlack, etc.)
  - Applies whitelist filtering to all downloaded content
  - Supports custom merging and wildcard prefixing
  - Handles various host file formats (0.0.0.0/127.0.0.1/plain domains)

### `sync-mirrors.sh`
- **Purpose**: Master script to update all mirrors and blocklists
- **Process**:
  1. Updates tracker-radar repository
  2. Runs `ddg.py` (DuckDuckGo processing)
  3. Runs `exodus.py` (Exodus Privacy processing)
  4. Runs `mirror.py` (external blocklist mirroring)
  5. Commits changes to git

## File Structure

```
scripts/
├── whitelist-manual          # Manually curated whitelist entries
├── whitelist-sets           # URLs to external whitelist sources
├── whitelist               # Generated master whitelist (output)
├── whitelist-subdomains    # Subdomain patterns for filtering
├── gen-whitelist.py        # Whitelist generator
├── ddg.py                 # DuckDuckGo tracker processor
├── exodus.py              # Exodus Privacy processor
├── mirror.py              # External blocklist mirror
└── sync-mirrors.sh        # Master sync script
```

## Troubleshooting

### Issue: Whitelisted domains still appear in mirrors
**Solution**: Ensure you've run the complete workflow:
1. Add domain to `scripts/whitelist-manual`
2. Run `./gen-whitelist.py` to update master whitelist
3. Run `./sync-mirrors.sh` to regenerate all mirrors with filtering applied

### Issue: Scripts fail to load whitelist
**Cause**: Missing `scripts/whitelist` file
**Solution**: Run `./gen-whitelist.py` first to generate the master whitelist

## Recent Changes

- **2025-09-10**: Modified `mirror.py` to respect whitelist filtering like other scripts
- All downloaded blocklists now have whitelisted domains filtered out before being saved to mirrors

## Commands Reference

```bash
# Generate master whitelist
./scripts/gen-whitelist.py

# Sync all mirrors (recommended)
./scripts/sync-mirrors.sh

# Individual script runs (if needed)
./scripts/ddg.py
./scripts/exodus.py  
./scripts/mirror.py
```