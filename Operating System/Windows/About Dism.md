[craeted at]: 20250629 04:32~10:04
### origin_by : https://learn.microsoft.com/en-us/powershell/module/dism/?view=windowsserver2025-ps

Dism(Deployment Image Servicing and Management) 플랫폼은 배포전에 Windows 이미지를 마운트 및 서비스에 사용됩니다. DISM 명령어의 부분집합은 온라인 Windows 이미지를 사용할 수 도 있습니다. DISM을 Windows 이미지(.wim) 파일이나 가상 물리 디스크(.vhd or .vhdx)에 대한 정보를 가져오거나 마운트하기 위해 사용할 수 있습니다. 또한 Windows 이미지나 개인수정판 이미지에 Windows 기능, 패키지, 드라이버를 설치, 제거, 설정, 갱신으로 Dism을 사용할 수 있습니다.
DISM 플랫폼은 dism.exe나 DISM API, command-line tool 또한 포함합니다. command-line tool은 Windows ADK(Assessment and Deployment Kit)를 가능하게하며 국제 설정을 위한 서비스 명령을 지원하는 기능이 추가로 포함되어있다.
"\$?[^1]"를 확인하는 걸로 DISM cmdlets[^2]가 작동중일 때 에러를 확인할 수 있다. 만약 True라면, 마지막 명령이 성공한 것이고. 거짓이면 마지막 명령이 실패한 것이다. "\$LASTEXITCODE"는 마지막으로 실행한 Win32의 실행에 대한 exit code를 저장한다. 예를 들어, "E:\images\c.wim"에 포함된 Windows image에 대한 정보를 "Get-WindowsImage"cmdlet이 가져오는지 못하는지 확인하려면, 다음을 참조
```PowerShell
try {
	Get-WindowsImage -ImagePath E:\images\c.wim
}
catch {
	$message = "TRAPPED: {0}: '{1}'" -f ($_.Exception.GetType().FullName), ($_.Exception.Message)
	Write-host $message
}
```

[^1]: https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_automatic_variables?view=powershell-7.4#section-1
[^2]:PowerShell에서 특정 작업을 수행하는 기본단위 https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/use-dism-in-windows-powershell-s14?view=windows-11