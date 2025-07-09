# System-Hardening
These configurations are not ones that are necessarily shown on Nessus because this is more of a system hardening type of exercise.
</br>
### Steps taken to create a vulnerable system:
- Disable Windows Defender
- Disable bitlocker
- Disable Windows Firewall
- Pause system updates
- Disable guest account

This script essentially reverses the insecure configurations:
```
# Ensure the script is running as Administrator

Write-Output "Starting system configuration tasks..."

# 1. Enable Windows Defender (Real-time protection)
Try {
    Set-MpPreference -DisableRealtimeMonitoring $false
    Write-Output "Windows Defender real-time protection enabled."
} Catch {
    Write-Warning "Failed to enable Windows Defender: $_"
}

# 2. Enable BitLocker on C: drive
Try {
    $bitlockerStatus = Get-BitLockerVolume -MountPoint "C:"
    if ($bitlockerStatus.ProtectionStatus -ne "On") {
        Enable-BitLocker -MountPoint "C:" -EncryptionMethod XtsAes256 -UsedSpaceOnly -TpmProtector
        Write-Output "BitLocker encryption initiated on C: drive."
    } else {
        Write-Output "BitLocker is already enabled on C: drive."
    }
} Catch {
    Write-Warning "Failed to enable BitLocker: $_"
}

# 3. Enable Windows Firewall for all profiles
Try {
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
    Write-Output "Windows Firewall enabled for all profiles."
} Catch {
    Write-Warning "Failed to enable Windows Firewall: $_"
}

# 4. Unpause (resume) Windows Updates
Try {
    $WUA = New-Object -ComObject "Microsoft.Update.AutoUpdate"
    $WUA.Resume()
    Write-Output "Windows Updates resumed."
} Catch {
    Write-Warning "Failed to resume Windows Updates: $_"
}

# Enable Guest account
Try {
    net user guest /active:no
    Write-Output "Guest account disabled."
} Catch {
    Write-Warning "Failed to enable Guest account: $_"
}

# Clean up
Remove-Item $tempInf -ErrorAction SilentlyContinue

Write-Output "All tasks completed."
```
![image](https://github.com/user-attachments/assets/2d88ff81-cd46-421c-884f-c5cf5b853d39)

## Conclusion
Although there are many more secure configurations that could be implemented, these are just some that bring a system to a more secure state.
