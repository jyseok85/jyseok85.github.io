# 개인정보 제거 및 메신져 연동

회사에서 운영중인 원격 프로그램이 ISMS 인증 대상이 됨으로 인해서, 개발 외의 작업이 너무 많이 생겼습니다. &#x20;



그래서 기존에 입력받던 개인 정보를 전부 제거하고, 사내메신저 연동을 시켰습니다.&#x20;



기존에 원격 프로그램을 사용하기 위해서는 별도로 회원가입을 해야했고, &#x20;

아이디 + 패스워드 + 암호화키 를 통해서 DB에 저장을 했었습니다.&#x20;



그러나 메신져 연동을 함으로써&#x20;

메신져아이디 + 제품키 + 비대칭암호화 키를 통해서 저장을 함으로써 유저를 구분하고 사용할 수 있도록 했습니다.&#x20;



원래라면 메신져 로그인 이후 넘겨주는 고유키를 등록해서 처리해야 하지만, 사내 메신져에서 그런건지원하지 않기 때문에 임의로 처리했습니다.&#x20;



이렇게 개인정보를 전부 제거하고,  매 신규사원 입사시 회원가입절차도 삭제했지만,  ISMS 인증에서 빼주지않고, 매달 번거롭게 체크하라고 하네요..