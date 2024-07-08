2FA 설명

Two-factor authentication, or 2FA, is a safety measure that requires users to confirm their identity using two separate authentication factors. Usually, these factors consist of two things: something the person possesses (like a security token) and something they know (like a password). By making it more difficult for unauthorized users to access accounts, even if they have the password, it provides another layer of security. By using this technique, the potential for password theft or breaches leading to illegal access is reduced.




**엔드포인트 직접 액세스**

응용프로그램의 모든 엔드포인트를 기록하고 2FA 인증 프로세스 전에 해당 엔드포인트에 직접 액세스합니다.
```
https://test.test.com/2FA/auth => https://test.test.com/dashboard
```

실패 시 Referrer 헤더를 OTP인증 링크로 변경합니다.

```
1. 다른 인증 엔드포인트(내 정보, 글 쓰기, 등 기타)페이지에 접근합니다.
2. Referrer 헤더를 계정 로그인 시 사용하는 OTP인증 링크로 변경합니다.
3. Referrer: [https://www.test.com/login.php?otp&test=otptesting](https://test.test.com/2FA/auth)
4. 우회가능 여부를 확인합니다.
```

**반응 조작**
```
{OTP:’23243′} => {OTP:’true’}
```
```
{mfa:’true’} => {mfa:’false’}
```

**2. OTP 파라미터 제거를 통한 2FA 우회**
```
{email:'abc@abc.abc', 비밀번호:'****', otp:'323232'} => {email:'abc@abc.abc', 비밀번호:'****'}
```

**3. 레이스 조건을 이용한 2FA 우회 Bypassing 2FA using Race Conditions** 

계정 내에서 인증을 위해 이전에 사용되었거나 사용되지 않은 토큰 값을 사용하여 장치를 2FA 우회 가능여부 확인

```
계정 로그인 후 현재 사용하고 있는 토큰 값을 사용하여 새로운 브라우저에서 2fa 우회 가능한지 확인

1. 계정에 로그인합니다.
2. OTP 코드를 인증합니다.
3. 새로운 브라우저에서 이전에 사용한 OTP 코드를 입력합니다.
4. 우회 되는지 확인합니다.

사용되지 않은 토큰 값을 사용하여 2FA 우회 가능여부 확인
1. 계정에 로그인합니다.
2. OTP 코드를 두개 발행합니다. 
3. 사용하지 않은 나머지 하나를 사용합니다.
4. 우회 되는지 확인합니다.
```

그러나 이 기법을 사용하려면 공격자가 이전에 생성된 값에 액세스할 수 있어야 하며, 이는 코드 생성 앱의 알고리즘을 뒤집거나 이전에 알려진 코드를 가로채는 방식으로 수행할 수 있습니다.

**4. OTP에서 무결성 검사 누락**

자신의 계정에서 토큰을 추출하여 다른 계정에서 2FA를 우회하는 것을 시도할 수 있습니다.

```
a. 공격자가 자신의 계정에 유효한 OTP를 요청합니다.
b. 공격자는 자신의 계정의 요청 OTP를 사용하여 Victimate 계정에 로그인합니다.
c. 응용 프로그램이 어떤 사용자에 대해 OTP가 유효한지 확인하지 않고 OTP가 유효한지 여부만 확인하면 이를 활용하여 MFA 제한을 우회할 수 있습니다.```
```

**5. 응답값 상태코드 조작을 이용한 2FA 우회**
```
{success:'false'} => {success:'true'}
```
```
{valid:'false'} => {valid:'true'}
```
```
{success:'0'} => {success:'1'}
```
```
HTTP/1.1200 OK로 응답 상태값 변경
```


**8. 비밀번호 변경/이메일 변경 시 2FA가 비활성화 되는지 여부 확인**

비밀번호 변경 링크 및 이메일 변경 링크, 인증확인 링크등을 사용하여 변경 시 2FA 가 우회되는지 확인

```
1. 비밀번호 및 재설정 링크를 보냅니다.
2. 재설정 링크를 사용하여 인증을 진행합니다.
3. OTP가 비활성화 되는지 확인합니다.
```

**9. OAuth 플랫폼 타협**
```
1. 피해자의 계정에 로그인하고 해당 로그인 자격증명에 내 2FA를 사용하여 사용자 자격증명을 잠금
2. 피해자는 자신의 계정을 찾기위해 OTP를 확인하지만 공격자가 제어하고 있어 불가능합니다.
```

**10. 브루트 포스 공격**
```
요금 제한 부재
코드 시도 횟수에 대한 제한이 없기 때문에 무차별 대입 공격이 가능하지만 잠재적인 무음 속도 제한을 고려해야 합니다.
```

**11. 이전 세션 처리**
```
2FA가 활성화되면 생성된 이전 세션이 종료되어야 합니다.고객이 계정을 손상시켰을 때 2FA를 활성화하여 보호하고 싶어할 수 있지만 이전 세션이 종료되지 않으면 이로 인해 보호되지 않기 때문입니다.
```

**12. 백엔드에서 우회**

동일한 세션을 사용하여 계정과 피해자 계정을 사용하여 흐름을 시작합니다. 두 계정으로 2FA 지점에 도달하면 계정으로 2FA를 완료하되 다음 부분에 접근하지 마십시오. 대신 피해자 계정 흐름으로 다음 단계에 액세스하십시오. 백엔드에서 세션 내에 성공적으로 2FA를 통과했다는 부울만 설정하면 피해자의 2FA를 우회할 수 있습니다.
```
1. 브라우저 A,B를 사용하여 OTP 인증 페이지에 접근합니다.
2. 브라우저 A의 OTP 인증을 활성화합니다.
3. 브라우저 B의 OTP 인증이 무력화 되어 추가 OTP 인증 없이 로그인이 되는지 확인합니다.
```

**13. 2FA에서 CSRF 비활성화: 2FA 비활성화 시 CSRF 보호 기능이 없으며 인증 확인 기능도 없습니다.**

```
1. 2FA 비활성화 시 추가 비밀번호 확인 인증등이 있는지 없는지 여부 확인
```


**2FA페이지 정보공개**

```
2FA 확인 페이지의 민감한 정보 공개(예: 전화번호)가 되는지 확인
```

이중 요소 인증은 중요한 보안 조치이지만 공격자가 사용하는 우회 기술에 대한 정보를 유지하는 것이 중요합니다. 
이 동작은 로그인 프로세스에 보안 계층을 추가하는 2FA의 목적을 무시하므로 응용 프로그램의 논리에 심각한 결함이 있음을 나타냅니다.


2FA 코드 재사용

