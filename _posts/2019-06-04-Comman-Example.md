---
title: "[C#] WPF Command 기본 사용"
date: 2019-06-04T12:51:00-05:00
excerpt: "C# WPF에서 Command 기본 사용법과 예제"
categories:
  - programming
tags:
  - csharp
  - wpf
  - command
  - relaycommand
link: https://github.com/baejun-k/CommandExample
---  

c#에서 ICommand 인터페이스를 통해 쉽게 사용가능하고, ICommand를 통한 Command 기본 사용법과 예제를 포스팅한다.  
WPF 프로젝트로 작성된 예제임.

### c# ICommand  

``` csharp
namespace System.Windows.Input {
	public interface ICommand {
  		event EventHandler CanExecuteChanged;
		bool CanExecute(object parameter);
		void Execute(object parameter);
	}
}
```

매우 짧게 설명하면 특정 작업을 정의하고 클래스화시켜서 사용하는 형태인데 자세한 설명은 다른 곳을 참조.  

주로 사용할 때는 UI 작업에서 버튼 클릭 시 작업하는 Command에 많이 엮어주는데, 경험상 편했던 점은 Command 작업을 진행 중에는 해당 버튼이 비활성화되어 이벤트처리에 귀찮은 것도 많이 줄었었고, 최근 mvvm 패턴을 공부하며 작업을 진행할 때 Command로 많이 했었는데 뭔가 잘 맞는 느낌이었다.  
기타 Command 사용패턴이 작업순서관리(Undo/Redo 같은?)같은 곳에서도 유용하다고하니 프로그래머가 알맞게 잘 사용하면 될 것같다.  
  
##  
### Command 예제  

작업 시간이 긴 Command를 버턴에 엮어두면 당연히 UI thread가 멈추기 때문에 길다 싶은 작업은 비동기 Command로 사용해야하고, 이때는 async 키워드를 Execute에 붙여서 구현해주면 된다.  
  
  __기본 Command__
  ``` csharp
  public class RelayCommand : ICommand {
	private readonly Action<object> _handler;
	private bool _canExecute = true;

	/// <summary>
	/// 특정 행동을 하는 핸들러를 받아 생성
	/// </summary>
	/// <param name="handler"></param>
	public RelayCommand(Action<object> handler)
	{
		_handler = handler;
	}

	private void SetCanExecute(bool canExecute)
	{
		if (_canExecute != canExecute) {
			_canExecute = canExecute;
			CanExecuteChanged?.Invoke(this, EventArgs.Empty);
		}
	}

	public event EventHandler CanExecuteChanged;

	public bool CanExecute(object parameter) => _canExecute;

	public void Execute(object parameter)
	{
		if (CanExecute(null) == false) { return; }
		SetCanExecute(false);
		_handler?.Invoke(parameter);
		SetCanExecute(true);
	}
}
```


__비동기 Command__

``` csharp
public class AsyncRelayCommand : ICommand {
	private readonly Func<object, Task> _handler;
	private bool _canExecute = true;

	/// <summary>
	/// 특정 행동을 하는 핸들러를 받아 생성
	/// </summary>
	/// <param name="handler"></param>
	public AsyncRelayCommand(Func<object, Task> handler)
	{
		_handler = handler;
	}

	#region CanExecute를 다시 확인하도록하는 다른 방법
	//private void SetCanExecute(bool canExecute)
	//{
	//	if (_canExecute != canExecute) {
	//		_canExecute = canExecute;
	//		RaiseCanExecuteChanged();
	//	}
	//}

	//public event EventHandler CanExecuteChanged {
	//	add { CommandManager.RequerySuggested += value; }
	//	remove { CommandManager.RequerySuggested -= value; }
	//}

	///// <summary>
	///// 비동기 작업 상태가 바뀌면 CanExecute를 다시 확인하도록...
	///// </summary>
	//private static void RaiseCanExecuteChanged()
	//{
	//	CommandManager.InvalidateRequerySuggested();
	//}
	#endregion

	private void SetCanExecute(bool canExecute)
	{
		if (_canExecute != canExecute) {
			_canExecute = canExecute;
			CanExecuteChanged?.Invoke(this, EventArgs.Empty);
		}
	}

	public event EventHandler CanExecuteChanged;

	public bool CanExecute(object parameter)
	{
		return _canExecute;
	}

	public async void Execute(object parameter)
	{
		if (CanExecute(null) == false) { return; }
		if (_handler != null) {
			SetCanExecute(false);
			await _handler(parameter);
			SetCanExecute(true);
		}
	}
}
```

__스크린샷__

<figure style="width: 150px" class="align-left">
  <img src="/assets/images/PostCommandSample/command_sample1.PNG" alt="">
  <figcaption>기본화면</figcaption>
</figure> 

<figure style="width: 150px" class="align-left">
  <img src="/assets/images/PostCommandSample/command_sample2.PNG" alt="">
  <figcaption>Command 클릭</figcaption>
</figure> 

<figure style="width: 150px" class="align-left">
  <img src="/assets/images/PostCommandSample/command_sample3.PNG" alt="">
  <figcaption>비동기 Command 작업 중</figcaption>
</figure> 

<figure style="width: 150px" class="align-left">
  <img src="/assets/images/PostCommandSample/command_sample4.PNG" alt="">
  <figcaption>비동기 Command 작업 종료 후</figcaption>
</figure> 
