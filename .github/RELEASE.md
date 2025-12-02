# Release Process

This document describes how to release new versions of python-rigid to PyPI.

## Prerequisites

### Set up PyPI Trusted Publishing

For secure, token-free publishing to PyPI, configure trusted publishing:

1. Go to [PyPI Account Settings](https://pypi.org/manage/account/publishing/)
2. Click "Add a new pending publisher"
3. Fill in the details:
   - **PyPI Project Name**: `python-rigid`
   - **Owner**: `bahadrix`
   - **Repository name**: `rigid`
   - **Workflow name**: `publish.yml`
   - **Environment name**: `pypi`

4. (Optional) For TestPyPI, repeat the process at [TestPyPI Publishing](https://test.pypi.org/manage/account/publishing/) with environment name `testpypi`

### Alternative: Using API Tokens

If you prefer to use API tokens instead:

1. Generate an API token at [PyPI](https://pypi.org/manage/account/token/)
2. Add it as a repository secret named `PYPI_API_TOKEN` in GitHub
3. Modify the publish workflow to use the token instead of trusted publishing

## Release Steps

### 1. Update Version

Edit `pyproject.toml` and update the version number:

```toml
[project]
version = "0.1.4"  # Update this
```

### 2. Update CHANGELOG (if you have one)

Document the changes in this release.

### 3. Commit and Push

```bash
git add pyproject.toml
git commit -m "Bump version to 0.1.4"
git push origin main
```

### 4. Create a Git Tag

```bash
git tag v0.1.4
git push origin v0.1.4
```

### 5. Create a GitHub Release

1. Go to [GitHub Releases](https://github.com/bahadrix/rigid/releases)
2. Click "Draft a new release"
3. Choose the tag you just created (`v0.1.4`)
4. Fill in the release title and description
5. Click "Publish release"

The `publish.yml` workflow will automatically:
- Build the package
- Run tests (via the test workflow)
- Publish to PyPI

### 6. Verify the Release

Check that the new version appears on:
- [PyPI Project Page](https://pypi.org/project/python-rigid/)
- [GitHub Releases](https://github.com/bahadrix/rigid/releases)

## Testing Releases

### Manual TestPyPI Release

To test the release process without publishing to production PyPI:

1. Go to [Actions](https://github.com/bahadrix/rigid/actions)
2. Select the "Publish to PyPI" workflow
3. Click "Run workflow"
4. The package will be published to TestPyPI

### Install from TestPyPI

```bash
pip install --index-url https://test.pypi.org/simple/ python-rigid
```

## Troubleshooting

### Build Failures

If the build fails:
1. Check the [Actions](https://github.com/bahadrix/rigid/actions) tab for error logs
2. Ensure all tests pass locally: `python -m unittest discover`
3. Verify the build works locally: `python -m build`

### Publishing Failures

If publishing fails:
1. Verify trusted publishing is configured correctly on PyPI
2. Check that the version number in `pyproject.toml` hasn't been used before
3. Ensure the workflow has the correct permissions

## Workflows

### test.yml

Runs on every push and pull request to `main`:
- Tests on Python 3.11, 3.12, 3.13
- Tests on Ubuntu, macOS, and Windows
- Uses `uv` for fast dependency installation

### publish.yml

Runs when a release is published:
- Builds the package using `hatchling`
- Publishes to PyPI using trusted publishing
- Optional: Manual trigger for TestPyPI releases