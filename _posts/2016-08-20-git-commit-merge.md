---
layout: post
title: "GIT 여러 커밋을 하나로 합치기"
#description: ""
date: 2016-08-20
tags: [git]
comments: true
share: true
---


GIT 여러 commit을 하나로 합치기
===

버전 관리 시스템은 변경 내역을 기록하고 보관 하기 위해 사용하는 만큼 이력들(commit log)를 잘 관리하는 것이 중요하다. 이를 위해서는 은근히 신경 써야 할 것 들이 많은데

1. 좋은 커밋 메시지를 작성한다.
2. 일련된 작업 단위의 커밋
3. 커밋 전략, 브런치 전략 등등

회사에서는 SVN을 사용하고 개인 작업용으로는 주로 Git을 사용했는데 최근 스터디 모임등으로 인해 Git으로 협업을 하게 되었다. 따라서 혼자 브런치를 가르고 합치는 등의 방법을 연습하고 있는데 아주 재미있다.

현재 진행하는 스터디 모임에서는 각자가 담당 도메인마다 브런치(Brunch)를 따서 작업을 하고, 한 주간 완료 된 작업은 다시 `develop` 브런치로 `Pull Request` 를 보내 반영하는 식으로 진행한다.

이 때 발생 한 문제는, 같은 내용이 여러 커밋에 거쳐 나눠진다는 것이다. 무슨 말이냐면

- 수요일에 일단 작업을 마무리 하고 커밋을 했다.
- 목요일날 생각해보니 조금 수정을 해야할 것 같다. 수정을 하고 커밋을 했다.
- 금요일날 또 생각해보니 수정을 해야할 것 같다. 수정을 하고 커밋을 했다.

최초에 기능을 개발한 후, 2번의 수정을 거쳤다. 물론 나의 입장 에서 보면 별개의 작업들이지만, 내 커밋 로그들이 여러 사람들의 커밋로그들이 모두 합쳐질 통합 브런치에 포함된다고 생각해보자.
그렇게 생각해보면 이 세 개의 커밋들은 결국 하나의 커밋으로 합쳐 통합 브런치에 포함하는 것이 낫다고 할 수 있다.

## 여러 커밋 하나로 합치기

이미 모든 작업들이 커밋 완료 된 상황이다. 나는 최근 2개의 커밋을 하나로 합칠 것이다.

```bash
$ git rebase -i HEAD~2
```

위의 명령어로 최근 현재 HEAD 부터 최근 2개의 커밋을 `rebase` 하라고 명령한다.
그럼 아래와 같은 화면이 나타난다.

![](https://lh3.googleusercontent.com/5uVhOFXnEVcjomrdC4ac8EcNAXfGULBWvKfzYoffvwaBX8tv6Muc_NuMNMBdHKw5Dt7sjClEevMvlwSkA1IIxzW7sFQ9_-9PIlVxGLp4JL2ksDwkSNRfxQlFA_GkMeViEicUYzy22_CUduOcsUH_DE5bLwUwpIkWOu2Txw6kQvUEJ6WD-Fs8dkeX2r0uH-M3vo9mKZzz1QZhFXJODzSAl_kKzNzG7xeB30M5dGYScYLyMSrvYtKN4uYINDioiwITXmjZ3OfsmNZQhrJa8OFpDcrJXPR_1EZ8zagEdLrGjPq6DHdAUcKqrbq-G5D8cBYJ4LyBU1X9ZAoM0onCp46JEDR4DMhXoZWtZQSPFA10hbt-pIX3QLWkIeXdtUzQRCg3BAejHFX9p91uTGdsxl7QHrlAaJ9vFmeUdDAezm-ZbvfhWudo0SjmFFlztnth0RFROulgJYzlKFaVYi-C8LYp5vjujuaOAAirbiKmu_6RcUWd77bZumzoe0lbUN0_l0BF2H5F66svj92Ee_RIUL-uGNWEZOJlPgxPoiZbS5Jfosvsccu4Ddgp9IuyTGq4jBjwIze0esX7GXRzPjmdPmG-8H2Ii80zNCzhc8UjlyiIWhSscKzo=w668-h272-no)

두번째 줄의 `pick`을 `squash`로 변경한 후 `!wq`. 그럼 합친 커밋의 커밋 메시지를 새로 작성할 수 있게 해준다. 아래와 같이 커밋 메세지를 새롭게 입력한 후 `!wq`

![](https://lh3.googleusercontent.com/M4uz1Mmol5IMDLpqUrD5sS6KIK9up3vSEYLZcZLTxOH1d_Ui6Cg0WGpNOEgZKiN_pc0mePIgptrK-z2b5kLWl64m-1q2YBiTae3c_F-CkHtsHHr2T2IZ-_SPAYbI9X-raaRWw1gxzjbhpaTnmqsQgFRlFOI8ZBok5g-LIhyLCpMVBfPlCX_-gACX-B5pyCtyEUWMJFvioMDSqRpBD-vVCcWohOz5HNOyOPCKksA6QHaddJN6l1McfgD7L5DlSuU6nFlkEpY8QaPC7OXf_znB0rJHxLUOrrF4Psdm1kzZ6SwWDrRUcSVBsw7_F6oeijKVsKz1zJwVaQzyKIKV2KSw5MISuyXYp-zkB1Ss166jW6ER4pasXTJj-svCbrnjR6Mtlpno7avrFi3iCnaR2IT2es2TH9VowdzmHzc2nHJUMAxRMp4XLTuWV5q4NoGI6nm0yJJyrf48iuKiRm-1zDG-1iogF-WnL6QteEtEpYCSh4qro9g3PBJpv57IlVrvncBEAc0yiHU3DdEcCLQjQjBGQKMJvS-dfN6fRgPiT9JxzJZEkZCiz2HyR7PLho-uo48q7dLay9IEHX7imkpKdXqLJ5UqkdOAfDbN0k_IJoeIigBcTza2=w587-h280-no)

이제 합친 커밋을 원격 저장소에 `push` 를 하며 오류가 발생한다.

![](https://lh3.googleusercontent.com/kYfnwS2gL1KAqgywyeTL1IldOEQQ-4p_k_EAuwqe9Dl5AI5YjD3_RpIdEw9rUoHOz37jvQawe0VnfPhG8K4dDmjkAugVC_E_UnL9vgTLcS4oT2HPQsL4E5JKT83mvbFJc-E5Iz_6HFgkSJGm_bYO_Va2T2osxagt7zaXAbm1vNbolK6aNG5nj_aEve1bVra-AGZDWa3gkzKVUvICL1j9LevcI4n4xdm7ftjTumXnqaZafsF3JE2mJn1qdSFMI0_lTC6KnCcUT1Fl3vR57GL6kvfUFNF6cMksUzKRlrI5dutNWR-8y4MVAy1-MIXhNtU5M-z-MPQfxG6qOkDHi4gT5hUGDNWFIQJL_DRBrUvS41UKf1FAqrEexOxZBZqQwVzI48gsCz6Xtr1inYO1BvhRKC0aDo91D2WN-TuQtI0QkDTYO9-1uZPLUtwVefd4y8J0HTFCgG3hvl7fhh-TI-swN3Qvdh9NNUkoQEqLW0TIkakd6zIpWtUnRlPgwq9UUTHjZ7b2X31yfUUn_bla0QOzTAkDTbCN8olSYZfm0TSbd4IOEi5IuuN-lNG4ICKlJu1ThMqU8WmtpRFzyDbcqJW3U5rBJ0cF_XnrMWVLNCWYNn1Bn8Yz=w668-h138-no)

왜냐하면 히스토리로 봤을 때는 현재 로컬저장소에서 원격저장소로 푸쉬하려는 커밋은 HEAD가 더 뒤에 있기 때문이다. 위에서 커밋을 합치는 과정에서 더 이전의 커밋으로 합치는 과정을 거쳤기 때문인듯 하다. 이때에는 브런치명 앞에 `+` 를 포함한다.

```bash
$ git push origin +브런치명
```


## 참고자료

- [Git - 히스토리 단장하기](https://git-scm.com/book/ko/v1/Git-%EB%8F%84%EA%B5%AC-%ED%9E%88%EC%8A%A4%ED%86%A0%EB%A6%AC-%EB%8B%A8%EC%9E%A5%ED%95%98%EA%B8%B0)
- [[입 개발]GIT: 여러 개의 commit 하나로 합치기](https://charsyam.wordpress.com/2013/01/11/%EC%9E%85-%EA%B0%9C%EB%B0%9Cgit-%EC%97%AC%EB%9F%AC-%EA%B0%9C%EC%9D%98-commit-%ED%95%98%EB%82%98%EB%A1%9C-%ED%95%A9%EC%B9%98%EA%B8%B0/)
