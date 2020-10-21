---
title: "Flutter에서 모든건 Widget이다. Show/Hide까지도."
date: 2020-09-11
cagegory: 
 - flutter
tags: 
 - flutter
 - widget
 - visibility
---

![](https://sh0seo.github.io/img/flutter-widget-all.png)
         
flutter에서 모든 건 Widget입니다. 간단한 문자를 출력하는 [Text](https://api.flutter.dev/flutter/widgets/Text-class.html)부터, 다른 Widget을 담기 위한 [Container](https://api.flutter.dev/flutter/widgets/Container-class.html)까지도 모두 Widget입니다. 한마디로 사용자에게 보이든, 보이지 않든 화면을 구성하는 모든 것은 Widget입니다.

![](https://sh0seo.github.io/images/flutter-widget-container.png)

그리고 한발 더 나아가서 Widget 자체의 Show, Hide 까지도 별도의 Widget([Visibility](https://api.flutter.dev/flutter/widgets/Visibility-class.html))입니다.

## 다른 UI에서 Visibility

UI를 다루는 다른 프레임워크를 보면 일반적으로 A라는 컴포넌트를 사용자에게 Show, Hide 처리하는 것은 그 컴포넌트 자체에서 show(), hide() 함수(혹은 메소드)를 제공하거나 투명도를 0으로 설정하여 처리하곤합니다. 예를 들어 안드로이드에서 TextView는 xml에서 visibility를 설정할 수 있습니다.

```xml
<TextView
    ...
    android:visibility="gone"
/>
```

html에서는 대상의 style.display에 설정을 통해 show, hide를 처리합니다.

```
document.getElementById("element").style.display = "none";
```

## Flutter에서 Visibility는 Visibility Widget을 사용

그렇다면 flutter에서는 Visibility Widget으로 어떻게 show, hide를 구현 할까요? 아래와 같이 중앙에 Text 문구를 출력하는 앱이 있습니다. 플로팅 버튼을 클릭하면 앱이 사라지게 구현을 해봅시다.

![](https://sh0seo.github.io/img/flutter-widget-text.png)

```dart
class _MyHomePageState extends State<MyHomePage> {
  bool _visibility = true;

  void _show() {
    setState(() {
      _visibility = true;
    });
  }

  void _hide() {
    setState(() {
      _visibility = false;
    });
  }
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('widget.title'),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
              Text('이 글자를 안보이게 하고 싶습니다.',
                style: Theme.of(context).textTheme.headline4,  
              ),
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () => {_visibility? _hide() : _show()},
        tooltip: 'show/hide',
        child: Icon(Icons.add),
      ),
    );
  }
}
```

Text Widget을 **Visibility Widget**의 child로 감싸줍니다. 그리고 변경이 필요한 시점에 _visibility를 이용하여 상태를 Text Widget의 show/hide를 관리합니다.

```dart
children: <Widget>[
  Visiblity(
    child: Text('이 글자를 안보이게 하고 싶습니다.',
      style: Theme.of(context).textTheme.headline4,  
    ),
    visible: _visibility,
  ),
],
```

## 정리

Android, html의 show/hide와 달리 Visibility 자체도 Widget으로 따로 처리한다는 것이 낮설게 느껴졌지만 여백 같은 빈공간 조차도 [SizedBox Widget](https://api.flutter.dev/flutter/widgets/SizedBox-class.html)으로 구현해 놓은걸 보면 flutter가 skia로 랜더링하기 위해서 모든 것을 Widget화 시킨게 아닌가 추측됩니다.

## 참조

- https://stackoverflow.com/questions/44489804/show-hide-widgets-in-flutter-programmatically
- https://api.flutter.dev/flutter/widgets/Visibility-class.html
- https://pub.dev/packages/universal_widget
