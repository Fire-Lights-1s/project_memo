# 양방향 엔티티 주의 사항
## 무한 루프
`toString()`을 사용하거나 컨트롤러에서 JSON 형태로 응답을 반환하는 과정에서 발생할 수 있는 무한 루프를 주의해야 한다. 기본적으로 양방향 연관 관계는 두 엔티티가 서로를 참조하는 형태가 된다.




>JPA 연관관계 매핑
>주소 : https://colevelup.tistory.com/41

>양방향 엔티티 주의 사항
>주소 : https://colabear754.tistory.com/142

>양방향 엔티티 저장
>주소 : https://os94.tistory.com/195