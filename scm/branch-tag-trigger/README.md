# Jenkins Branch Specifier Trigger Exercise

## Goal
Configure Jenkins to trigger builds only for specific branches using **Branch Specifier** patterns.

## The Exercise: Branch Specifier Configuration

### Step 1: Create Freestyle Job
1. **New Item** → **Freestyle Project** → Name: `branch-specifier-demo`

### Step 2: Configure Source Code Management
1. **Git** → Add your repository URL
2. **Branches to build** → This is where the magic happens!

### Step 3: Branch Specifier Patterns (THE FOCUS)

Replace the default `*/master` with these patterns:

#### Pattern 1: Single Branch
```
*/main
```
**Result:** Only triggers on `main` branch

#### Pattern 2: Multiple Specific Branches  
```
*/main
*/develop
*/staging
```
**Result:** Triggers on main, develop, OR staging branches

#### Pattern 3: Wildcard Patterns
```
*/feature/*
```
**Result:** Triggers on ANY branch starting with `feature/`

#### Pattern 4: Complex Pattern
```
*/main
*/develop
*/feature/*
*/hotfix/*
```
**Result:** Triggers on main, develop, any feature branch, any hotfix branch

#### Pattern 5: Exclude Pattern
```
*
!*/experimental/*
```
**Result:** All branches EXCEPT experimental branches

### Step 4: Test Each Pattern

**Test Setup:**
Create these branches in your repo:
- `main`
- `develop` 
- `feature/login`
- `feature/payment`
- `hotfix/bug123`
- `experimental/test`

**Testing Process:**
1. Set branch specifier to `*/main`
2. Push to different branches
3. Check which ones trigger builds
4. Change pattern and repeat

### Step 5: Real-World Patterns

#### Production Pattern:
```
*/main
*/release/*
```
Only production and release branches trigger

#### Development Pattern:
```
*/develop
*/feature/*
*/bugfix/*
```
Development workflow branches

#### Everything Except:
```
*
!*/experimental/*
!*/temp/*
!*/personal/*
```
All branches except experimental, temp, personal

## Quick Test Results

| Branch Specifier | Branches That Trigger |
|------------------|----------------------|
| `*/main` | ✅ main |
| `*/feature/*` | ✅ feature/login, ✅ feature/payment |
| `*/main<br>*/develop` | ✅ main, ✅ develop |
| `*` | ✅ ALL branches |
| `!*/temp/*` | ✅ All EXCEPT temp branches |

## Key Points

**Branch Specifier = Trigger Filter**
- Controls WHICH branches can trigger builds
- Uses Git ref patterns
- Multiple patterns = OR logic
- `!` prefix = exclusion

**The Pattern Syntax:**
- `*` = wildcard (matches anything)
- `*/branch-name` = specific branch
- `*/prefix/*` = any branch starting with prefix
- `!*/pattern` = exclude this pattern

**Focus:** The branch specifier is your gatekeeper - it decides which branch pushes actually trigger Jenkins builds!