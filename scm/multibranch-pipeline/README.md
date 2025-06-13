# Multibranch Pipeline Branch Trigger Exercise

## Goal
Configure **Branch Discovery** filters in Multibranch Pipeline to control which branches trigger builds.

## The Exercise: Branch Discovery Configuration

### Step 1: Create Multibranch Pipeline
1. **New Item** → **Multibranch Pipeline** → Name: `multibranch-trigger-demo`

### Step 2: Configure Branch Sources
1. **Branch Sources** → **Git** → Add repository URL
2. **Discover branches** → This is where the trigger magic happens!

### Step 3: Branch Discovery Strategies (THE FOCUS)

#### Strategy 1: All Branches
```
✅ Discover branches: All branches
```
**Result:** Every branch triggers its own build job

#### Strategy 2: Branch Filter by Name
```
✅ Discover branches: Filter by name (with regular expression)
Include: main|develop|feature/.*
```
**Result:** Only main, develop, and feature/* branches trigger builds

#### Strategy 3: Exclude Branches
```
✅ Discover branches: Filter by name (with regular expression)  
Include: .*
Exclude: experimental/.*|temp/.*|personal/.*
```
**Result:** All branches EXCEPT experimental, temp, personal branches

### Step 4: Advanced Branch Discovery Patterns

#### Production Pattern:
```
Include: main|release/.*|hotfix/.*
Exclude: 
```
**Triggers:** main, release/*, hotfix/* only

#### Development Pattern:
```
Include: develop|feature/.*|bugfix/.*
Exclude: feature/experimental.*
```
**Triggers:** develop, feature/* (except experimental), bugfix/*

#### Team-Based Pattern:
```
Include: main|team-(backend|frontend)/.*
Exclude: team-.*/personal.*
```
**Triggers:** main, team-backend/*, team-frontend/* (except personal)

### Step 5: Property Strategy (Additional Filter)

Add **Branch Property Strategy:**
```
✅ All branches get the same properties
Or
✅ Named branches get different properties:
  - main: Deploy to production
  - develop: Deploy to staging  
  - feature/*: Run tests only
```

### Step 6: Test Setup

**Create these branches:**
- `main`
- `develop`
- `feature/login`
- `feature/experimental-ui`
- `experimental/test123`
- `personal/john-test`
- `release/v1.2.0`
- `hotfix/critical-bug`

### Step 7: Testing Branch Discovery

#### Test 1: Include Pattern
```
Include: main|develop|feature/.*
```
**Expected Builds:** ✅ main, ✅ develop, ✅ feature/login, ✅ feature/experimental-ui
**No Builds:** ❌ experimental/test123, ❌ personal/john-test

#### Test 2: Exclude Pattern  
```
Include: .*
Exclude: experimental/.*|personal/.*
```
**Expected Builds:** ✅ main, ✅ develop, ✅ feature/*, ✅ release/*, ✅ hotfix/*
**No Builds:** ❌ experimental/*, ❌ personal/*

#### Test 3: Complex Pattern
```
Include: main|release/.*|hotfix/.*
Exclude: 
```
**Expected Builds:** ✅ main, ✅ release/v1.2.0, ✅ hotfix/critical-bug
**No Builds:** ❌ develop, ❌ feature/*, ❌ experimental/*, ❌ personal/*

## Key Differences: Freestyle vs Multibranch

| Aspect | Freestyle | Multibranch Pipeline |
|--------|-----------|---------------------|
| **Pattern Location** | Branch Specifier | Branch Discovery |
| **Syntax** | `*/pattern` | `regex pattern` |
| **Jobs Created** | 1 job, multiple triggers | Multiple jobs, one per branch |
| **Pattern Field** | "Branches to build" | "Include/Exclude" filters |

## Quick Reference: Regex Patterns

| Pattern | Matches |
|---------|---------|
| `main` | Exactly "main" |
| `.*` | All branches |
| `feature/.*` | Any branch starting with "feature/" |
| `main\|develop` | main OR develop |
| `release/v[0-9]+.*` | release/v1.0, release/v2.1, etc. |
| `^(?!experimental).*` | All branches NOT starting with "experimental" |

## Verification Steps

1. **Check Jenkins Dashboard** → See which branch jobs were created
2. **Push to different branches** → Verify which ones trigger new builds
3. **Look at "Branch Indexing Log"** → See discovery decisions

**Focus:** Branch Discovery = "Which branches get their own build jobs?"
Unlike freestyle jobs, multibranch creates separate jobs for each discovered branch!