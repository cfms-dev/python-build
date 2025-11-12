# Migration from AppVeyor to GitHub Actions

## Overview
This document describes the migration from AppVeyor CI to GitHub Actions for building Python 3.14 across multiple platforms.

## Changes Made

### 1. New GitHub Actions Workflow
- **File**: `.github/workflows/build-python.yml`
- **Python Version**: Updated from 3.12.9 to 3.14.0
- **Platforms**: Android, Darwin (iOS/macOS), Linux, Windows

### 2. Workflow Structure
Each platform has its own job in the workflow:

#### Android Job (`build-android`)
- Runs on: `ubuntu-latest`
- Uses NDK r27
- Builds all ABIs: arm64-v8a, armeabi-v7a, x86_64, x86
- Packages for mobile-forge and Flutter/Dart

#### Darwin Job (`build-darwin`)
- Runs on: `macos-latest`
- Clones BeeWare's Python-Apple-support repository
- Builds iOS and macOS frameworks
- Packages for Flutter/Dart integration

#### Linux Job (`build-linux`)
- Runs on: `ubuntu-latest`
- Uses python-build-standalone from Astral
- Builds for x86_64 and aarch64 architectures
- Creates stripped install-only packages

#### Windows Job (`build-windows`)
- Runs on: `windows-latest`
- Downloads official Python 3.14.0 installer
- Creates custom distribution in C:\python314-dist
- Packages for Flutter/Dart integration

### 3. Triggers
The workflow is triggered by:
- Pushes to branches matching `python-3.*` or `main`
- Pull requests to branches matching `python-3.*` or `main`
- Manual workflow dispatch

### 4. Artifacts and Releases
- **Development builds**: Artifacts uploaded to GitHub Actions workflow runs
- **Tagged releases**: Automatically deploys to GitHub Releases when tags matching `v*` are pushed

### 5. Environment Variables
```yaml
PYTHON_VERSION: 3.14.0
PYTHON_VERSION_SHORT: 3.14
PYTHON_DIST_RELEASE: 20251007
```

## Key Differences from AppVeyor

| Feature | AppVeyor | GitHub Actions |
|---------|----------|----------------|
| Configuration | `.appveyor.yml` | `.github/workflows/build-python.yml` |
| Python version | 3.12.9 | 3.14.0 |
| Windows runner | Visual Studio 2022 | windows-latest |
| macOS runner | macos-sonoma | macos-latest |
| Linux runner | ubuntu-gce-c | ubuntu-latest |
| Artifact upload | `appveyor PushArtifact` | `actions/upload-artifact@v4` |
| Release deployment | AppVeyor GitHub provider | `softprops/action-gh-release@v2` |
| Secrets | AppVeyor encrypted token | GitHub's built-in `GITHUB_TOKEN` |

## Testing the Workflow

To test the new workflow:

1. Push changes to a branch matching `python-3.*` or `main`
2. Monitor the Actions tab in the GitHub repository
3. Check that all four jobs complete successfully
4. Verify artifacts are uploaded correctly

To test release deployment:

1. Create and push a tag: `git tag v3.14 && git push origin v3.14`
2. The workflow will automatically create a GitHub release with all artifacts

## Important Notes

### Python 3.14 Availability
- **Note**: Python 3.14.0 may not be released yet. The workflow is prepared for when it becomes available.
- If Python 3.14.0 is not available:
  - The Darwin build will fail if the BeeWare Python-Apple-support repository doesn't have a `3.14` branch
  - The Windows build will fail if the official Python installer is not available
  - Consider using Python 3.13.x as an interim version

### Adjustments for Current Python Versions
To use with Python 3.13 (currently available):
```yaml
env:
  PYTHON_VERSION: 3.13.1
  PYTHON_VERSION_SHORT: 3.13
  PYTHON_DIST_RELEASE: 20250205
```

And update Windows installation path:
- Change `C:\python314-dist` to `C:\python313-dist` in the Windows job

## Rollback Procedure

If issues arise, the original AppVeyor configuration is preserved as `.appveyor.yml.archived` and can be restored:

```bash
mv .appveyor.yml.archived .appveyor.yml
git add .appveyor.yml
git commit -m "Restore AppVeyor configuration"
```

## Future Improvements

Potential enhancements for the workflow:
1. Add caching for dependencies to speed up builds
2. Implement matrix strategy for testing multiple Python versions
3. Add workflow status badges to README
4. Set up notifications for build failures
5. Add code signing for Darwin builds
6. Implement incremental builds to reduce build times

## Support

For issues or questions about the migration:
1. Check the Actions tab for build logs
2. Review this migration document
3. Refer to GitHub Actions documentation: https://docs.github.com/en/actions
