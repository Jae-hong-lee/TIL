**CORS **
    
    **CORS는 무엇인가요?**
    
    Cross-Origin Resource Sharing(CORS)은 추가적인 HTTP header를 사용해서 애플리케이션이 다른 origin의 리소스에 접근할 수 있도록 하는 메커니즘을 말합니다. 하지만 다른 origin에서 내 리소스에 함부로 접근하지 못하게 하기 위해 사용된다.
    
    요청 헤더 목록에는 다음과 같이 있습니다.
    
    - Origin
    - Access-Control-Request-Method
        - `preflight`요청을 할 때 실제 요청에서 어떤 메서드를 사용할 것인지 서버에게 알리기 위해 사용됩니다.
    - Access-Control-Request-Headers
        - `preflight`요청을 할 때 실제 요청에서 어떤 header를 사용할 것인지 서버에게 알리기 위해 사용됩니다.
    
    응답 헤더들은 다음과 같이 있습니다.
    
    - Access-Control-Allow-Origin
        - 브라우저가 해당 origin이 자원에 접근할 수 있도록 허용합니다. 혹은 ``은 credentials이 없는 요청에 한해서 모든 origin에서 접근이 가능하도록 허용합니다.
    - Access-Control-Expose-Headers
        - 브라우저가 액세스할 수있는 서버 화이트리스트 헤더를 허용합니다.
    - Access-Control-Max-Age
        - 얼마나 오랫동안 `preflight`요청이 캐싱 될 수 있는지를 나타낸다.
    - Access-Control-Allow-Credentials
        - `Credentials`가 true 일 때 요청에 대한 응답이 노출될 수 있는지를 나타냅니다.
        - `preflight`요청에 대한 응답의 일부로 사용되는 경우 실제 자격 증명을 사용하여 실제 요청을 수행 할 수 있는지를 나타냅니다.
        - 간단한 GET 요청은 `preflight`되지 않으므로 자격 증명이 있는 리소스를 요청하면 헤더가 리소스와 함께 반환되지 않으면 브라우저에서 응답을 무시하고 웹 콘텐츠로 반환하지 않습니다.
    - Access-Control-Allow-Methods
        - preflight`요청에 대한 대한 응답으로 허용되는 메서드들을 나타냅니다.
    - Access-Control-Allow-Headers
        - `preflight`요청에 대한 대한 응답으로 실제 요청 시 사용할 수 있는 HTTP 헤더를 나타냅니다.
    
    **CORS는 왜 필요한가요?**
    
    만약 내가 서비스하고 있지 않은 사이트에서 세션을 요청해서 세션을 획득할 수 있다면 해당 사이트는 악의적으로 내 세션을 탈취하거나 다른 행동을 할 수 있습니다. 그래서 브라우저에서는 이러한 요청을 막습니다. 피싱사이트가 대표적인 공격 사례인데 이러한 것을 막고 내가 허용한 origin들만 요청할 수 있도록 하기 위해 필요합니다.
    
    **CORS는 어떻게 동작하나요?**
    
    브라우저가 리소스를 요청할 때 추가적인 헤더에 정보를 담습니다. 내 origin은 무엇이고 어떤 메소드를 사용해서 요청을 할 것이고 어떤 헤더들을 포함할 것인지를 담아서 서버에 전송합니다. 서버는 서버가 응답할 수 있는 origin들을 헤더에 담아서 브라우저에게 보냅니다. 브라우저가 이 헤더를 보고 해당 origin에서 요청할 수 있다면 리소스 전송을 허용하고 만약 불가능하다면 에러를 발생시킵니다.
