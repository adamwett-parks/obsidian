### Overview
- Backend ()
	- Slim (PHP)
	- MariaDB
- Frontend runs outside of Docker, contains client-side & server-side code
	- Server side: All API calls,  
	- Client side: interactive components (buttons, inputs, etc.)

### Clone Repo
- clone repo & switch to V2-167
	- we should take the current state of main and make it a different branch
	- pull the new V2 structure into V2-167, then V2-167 should become main
	- should clean up git history too while we're at it

### Add secrets (local files)
- TODO


### Installing Nodejs
1. Run this script in your PowerShell (Terminal)
```ps
# Define variables
$nodeVersion = "v22.21.0"
$nodeUrl = "https://nodejs.org/dist/$nodeVersion/node-$nodeVersion-win-x64.zip"
$downloadPath = "$env:TEMP\node-$nodeVersion-win-x64.zip"
$extractPath = "$env:USERPROFILE\nodejs"

# Download Node.js binary
Write-Host "Downloading Node.js $nodeVersion..." -ForegroundColor Green
Invoke-WebRequest -Uri $nodeUrl -OutFile $downloadPath

# Create nodejs directory if it doesn't exist
if (Test-Path $extractPath) {
    Write-Host "Removing existing nodejs directory..." -ForegroundColor Yellow
    Remove-Item -Path $extractPath -Recurse -Force
}
New-Item -ItemType Directory -Path $extractPath -Force | Out-Null

# Extract the zip file
Write-Host "Extracting Node.js to $extractPath..." -ForegroundColor Green
Expand-Archive -Path $downloadPath -DestinationPath $env:TEMP -Force

# Move contents from the extracted folder to nodejs directory
$extractedFolder = "$env:TEMP\node-$nodeVersion-win-x64"
Get-ChildItem -Path $extractedFolder | Move-Item -Destination $extractPath -Force

# Clean up
Write-Host "Cleaning up temporary files..." -ForegroundColor Green
Remove-Item -Path $downloadPath -Force
Remove-Item -Path $extractedFolder -Recurse -Force

# Add to PATH if not already present
$currentPath = [Environment]::GetEnvironmentVariable("Path", "User")
if ($currentPath -notlike "*$extractPath*") {
    Write-Host "Adding $extractPath to user PATH..." -ForegroundColor Green
    [Environment]::SetEnvironmentVariable(
        "Path",
        "$currentPath;$extractPath",
        "User"
    )
    Write-Host "PATH updated successfully!" -ForegroundColor Green
    Write-Host "Please restart your terminal for changes to take effect." -ForegroundColor Yellow
} else {
    Write-Host "$extractPath is already in PATH." -ForegroundColor Cyan
}

# Verify installation
Write-Host "`nVerifying installation..." -ForegroundColor Green
& "$extractPath\node.exe" --version
& "$extractPath\npm.cmd" --version

Write-Host "`nNode.js installation complete!" -ForegroundColor Green
```
1. Download [Nodejs Standalone Binary](https://nodejs.org/en/download) (.msi file)
2. 