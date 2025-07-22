# Trivy Scan Scripts

## PowerShell Script for Windows
# scan-trivy.ps1

Write-Host "Scanning vulnerable-app with Trivy..." -ForegroundColor Yellow

# Basic vulnerability scan
Write-Host "Running basic vulnerability scan..." -ForegroundColor Cyan
trivy image vulnerable-app:latest

Write-Host "`n" -ForegroundColor White
Write-Host "Running high/critical vulnerabilities only..." -ForegroundColor Cyan
trivy image --severity HIGH,CRITICAL vulnerable-app:latest

Write-Host "`n" -ForegroundColor White
Write-Host "Generating JSON report..." -ForegroundColor Cyan
trivy image --format json --output trivy-results.json vulnerable-app:latest

Write-Host "`n" -ForegroundColor Green
Write-Host "Scan completed! Check trivy-results.json for detailed report."
