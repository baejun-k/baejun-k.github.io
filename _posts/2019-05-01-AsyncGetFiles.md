---
title: "[C#] DirectoryInfo.GetFiles 비동기."
date: 2019-05-01T02:53:00-05:00
excerpt: "windows에서 C#으로 개발하다 겪은 폴더 안의 대량의 파일이 있을 때 이 파일 이름들을 읽어오다 생긴 문제와 대량의 파일 이름을 읽으면서 비동기로 작업을 할 수 있게 해결한 방법에 대하여 작성."
categories:
  - programming
tags:
  - csharp
  - directoryinfo
  - getfiles
  - async
  - 비동기
  - 파일이름
  - cmd
---  
  
# 필요하게 된 이유  
  
처음 테스트를 할 땐 Directory.GetFiles()를 사용였는데 테스트 파일이 많지 않아 별문제가 없었다.  
하지만 실제 사용을 할 때 파일이 몇만 몇십만으로 넘어가고 네트워크 공유 폴더였는데 다 불러오는데 시간을 너무 많이 잡아 먹어 원하는 작업이 불가능했다.  
Directory.GetFiles()를 비동기로 돌린다 하여도 작업이 끝나는데 너무 오래걸려 GetFiles() 결과를 이용해야하는 경우 다음 작업을 진행할 수가 없었다. 
많은 파일을 빨리 읽어오는 것보다 파일이 많더라도 부분적으로 파일을 읽으며 부분 작업을 진행해야할 필요가 생겼다.  
  
# 해결방법 및 비동기 구현  
  
해결방법은 생각보다 쉰게 찾았다.  
콘솔창(cmd.exe)에서 폴더 안의 파일을 찾아보면 어떻게 될지 확인해본 결과.  
수십 개를 찾은 뒤 콘솔창에 뿌려주고 다음 수십 개를 찾는 작업을 하는 것이였다.  
이를 이용하여 C#에서 콘솔창을 사용하여 결과를 비동기로 얻어오게 만들면 되겠다 싶었다.  
  
* 기본 형태  
``` c#
public Task<string[]> AsyncGetFiles(this DirectoryInfo dir, Action<string> handler)
{
	return Task.Run(() => {
		List<string> result = new List<string>();

		Process proc = new Process();
		ProcessStartInfo procInfo = new ProcessStartInfo();
		procInfo.FileName = "cmd.exe";
		procInfo.Arguments = $"/C dir /a-d/b {dir.FullName}";
		procInfo.CreateNoWindow = true;
		procInfo.UseShellExecute = false;
		procInfo.RedirectStandardOutput = true;
		proc.StartInfo = procInfo;
		proc.OutputDataReceived += (s, e) => {
			if (string.IsNullOrWhiteSpace(e.Data)) { return; }
			result.Add(e.Data);
			handler?.Invoke(e.Data);
		};
		proc.Start();
		proc.BeginOutputReadLine();
		proc.WaitForExit();

		return result.ToArray();
	});
}
```  
기본형태에서는 폴더 아래의 파일을 읽어오는 명령어를 콘솔에 실행시키는 Process를 만든 후 결과로 출력되는 값을 읽어와 사용한다.  
비동기로 작업을 하며 파일이 부분적으로 찾아질 때마다 Handler를 통해 작업을 해주며 나중에 파일 찾기 완료 후 찾은 모든 파일이름을 배열로 반환한다.  
  
검색하다 중간에 취소도 가능하며 검색할 파일이름의 패턴도 정할 수 있게 확장하고 편하게 사용하기위해 Extension Method로 구현하였는데,  
이를 System.IO.Directory Class에 넣고 싶었지만 static class기 때문에 Extension Method 사용이 불가하다.  
그래서 DirectoryInfo Class의 [Extension Method](https://github.com/baejun-k/AsyncGetFiles/blob/master/AsyncGetFiles/DirectoryInfoExtensionClass.cs)로 확장하였다.  
  
* 결과  
![결과](/assets/images/asyncgetfilescapture.PNG)  
