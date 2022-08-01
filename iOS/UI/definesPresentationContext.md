# definesPresentationContext

뷰 컨트롤러 또는 하위 컨트롤러 중 하나가 뷰 컨트롤러를 표시할 때 이 뷰 컨트롤러의 뷰를 포함할지 여부를 나타내는 부울 값입니다.

뷰 컨트롤러를 표시하기 위해 UIModal PresentationStyle.currentContext 또는 UIModalPresentStyle.overCurrentContext 스타일을 사용하는 경우 이 속성은 뷰 컨트롤러 계층의 기존 뷰 컨트롤러가 실제로 새 컨텐츠에 포함되는지 제어합니다. 컨텍스트 기반 프레젠테이션이 발생하면 UIKit은 프레젠테이션 뷰 컨트롤러에서 시작하여 뷰 컨트롤러 계층 위로 이동한다. 이 속성의 값이 참인 뷰 컨트롤러를 찾으면 해당 뷰 컨트롤러에 새 뷰 컨트롤러를 표시하도록 요청합니다. 뷰 컨트롤러가 프레젠테이션 컨텍스트를 정의하지 않는 경우 UIKit는 창의 루트 뷰 컨트롤러에 프레젠테이션을 처리하도록 요청합니다.

이 속성의 기본값은 false입니다. UI Navigation Controller와 같은 일부 시스템에서 제공하는 뷰 컨트롤러는 기본값을 true로 변경합니다.

-> 정리하면 
1. 더이상 상위가 없는 루트 뷰컨트롤러의 뷰에서 표시

2. 최상위 뷰를 찾으러 올라가다가 해당 프로퍼티가 true인 것을 체크가 되면 해당 뷰컨트롤러의 뷰에 표시

3. 일반 뷰컨트롤러는 해당 프로퍼티가 기본적으로 false 이고, NavigationController은 기본적으로 true 