name: TaroTR

on:
  workflow_dispatch:

jobs:
  windows-runner:
    runs-on: self-hosted
    timeout-minutes: 360

    steps:
      - name: Check Windows
        shell: powershell
        run: |
          Write-Host "Computer: $env:COMPUTERNAME"
          Write-Host "User: $env:USERNAME"

      - name: Get Tailscale Address
        shell: powershell
        run: |
          $tailscale = "C:\Program Files\Tailscale\tailscale.exe"

          if (-not (Test-Path $tailscale)) {
            Write-Error "Tailscale belum terpasang."
            exit 1
          }

          $ip = & $tailscale ip -4

          if ([string]::IsNullOrWhiteSpace($ip)) {
            Write-Error "Alamat Tailscale tidak ditemukan."
            exit 1
          }

          Write-Host "Tailscale IPv4: $ip"
          "TAILSCALE_IP=$ip" >> $env:GITHUB_ENV

      - name: Show Connection Address
        shell: powershell
        run: |
          Write-Host ""
          Write-Host "================================="
          Write-Host " ADDRESS: $env:TAILSCALE_IP"
          Write-Host "================================="
