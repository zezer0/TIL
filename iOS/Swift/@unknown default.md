# @unknown default

## @unknown default란
Swift5에서 소개된 
개념, 이는 우리가 Swift enum과 일할 수 있는 새로운 방법이며 미래의 변화(새로운 case)에 대해 준비할 수 있도록 도와주는 방법입니다.

![스크린샷 2022-07-10 오후 10 12 46](https://user-images.githubusercontent.com/83381672/178146872-7ee34ef3-7963-4397-9b3b-7a368ed69ccc.png)
해당 코드에서는 알려진 모드 경우를 커버하지만 Warning이 발생한다. 경고 문구를 보면 미래의 새로운 케이스가 생길 수 있기 때문에 @unknown default를 통해 커버를 하라는 경고가 발생한다. 

## frozen enum
frozen enum이란 더 이상 변화가 일어나지 않는 enum을 말하고 non-frozen enum은 미래의 새로운 변화가 발생하는 enum을 말한다. non-frozen enum의 경우 미래의 새로운 변화에 대응하기 위해 @unknown default를 사용해야 한다.

## default와 @unknown default의 차이
default와 @unknown default는 기능적으로 동일하게 작용하지만 알려진 모든 케이스에 대응하지 않을 경우 @unknown default는 컴파일러가 경고한다.

> @unknown default는 미래의 변화에 대응하기 위한 대비이기 때문에 switch문에서 모든 케이스에 대응하지 않을 경우 경고 메시지가 나타난다.  
   ![스크린샷 2022-07-10 오후 10 25 10](https://user-images.githubusercontent.com/83381672/178146884-0645bc70-1fe7-428b-bb74-8d840285aebc.png)

> default는 대응하지 않는 모든 케이스에 대한 기본 동작을 제공하는 것이므로 경고 메시지가 발생하지 않는다.
  ![스크린샷 2022-07-10 오후 10 25 20](https://user-images.githubusercontent.com/83381672/178146885-6ce7eb08-5089-4d61-a196-59b2e529f351.png)
