# Largest known prime number: new record!

12월 21일에 [GIMPS][]를 통해 51번째 [메르센 소수][mersenne_prime]인 `2 ** 82589933 - 1`가 새로 발견되어 알려진 가장 큰 소수의 기록을 갈아치웠다.

## [메르센 소수][mersenne_prime]

`M_n = 2 ** n - 1 (n > 1)`의 꼴로 나타내어지는 소수를 메르센 소수라고 한다.
하필 **2** 인 이유는 2진수로 나타냈을 때 예쁘니까.. 는 아니고, 2보다 큰 수를 n번(n > 1) 제곱하고 1을 빼는 경우 소수가 나오지 않기 때문이다.

### [Lucas-Lehmer Primality Test (LLT)][LLT]

어떤 `M_n`이 주어졌을 때, 소수 여부를 판별하는 쓸만한 방법은 무엇이 있을까?
GIMPS에서는 이 알고리즘을 이용해서 판별하고 있다(*특히, 병렬화가 쉬워서 CUDA 버전이 압도적으로 빠르다).

```clojure
(use '[clojure.math.numeric-tower])
(defn LLT [n]
  (let [p (dec (expt 2 n))]
     (= 0
      (loop [s 4
            m p
            n (- n 2)]
      (if (= n 0) s
        (recur (mod (- (* s s) 2) m) m (dec n))
       )))))

(filter LLT (range 3 30))
# (3 5 7 13 17 19)
```

`TODO: 증명이 복잡해서 읽다가 말았다ㅜㅜ`

### TMI

이외에도 메르센 소수의 성질에는 재미있는 것이 많다.
`M_n`이 소수일 경우 `n` 역시 소수이고(역은 성립하지 않는다),
`M_n`이 소수일 경우 `M_n * (M_n + 1) / 2 = (2 ** (n - 1)) * (2 ** n - 1)`이 [짝수 완전수][perfect_number]이다(역도 성립).

여기서 완전수가 나오는게 너무 신기해서 증명도 한번 찾아보았는데,
약수의 합을 나타내는 `sigma` 함수가 multiplicative함을 이용해서 의외로 정말 간단하게(물론 모르면 엄청 어렵겠지만..)
보일 수 있었다.

** 1. p가 메르센 소수이면 `p(p+1)/2`는 짝수 완전수이다. **

`p = 2 ** n - 1`이 소수라고 가정하자.
그럼 `sigma(p) = p + 1` 이고, `sigma(2 ** (n - 1) * p) = (2 ** n - 1) * (p + 1) = (2 ** n - 1) * (2 ** n)` 으로
완전수임을 보일 수 있다.

** 2. `p(p+1)/2`가 완전수이면 `p`는 메르센 소수이다. **

`p(p+1)/2`가 완전수이므로 `sigpa(2 ** (x - 1) * p) = (2 ** x - 1) * sigpa(p) = (2 ** n) * p`이 성립한다.
여기서 `sigpa`는 약수의 합이기에 항상 양수 값을 가진다는걸 생각해보면
`sigpa(p)`이 `2 ** x - 1`의 배수여야 함을 알 수 있다.
나아가 `p = (2 ** x - 1) * k`라고 쓰고 이를 처음의 등식에 대입하면
`(2 ** x - 1) * sigpa(p) = (2 ** n) * p = (2 ** x - 1) * sigpa(p) = (2 ** n) * (2 ** x - 1) * k`
가 되는데, 여기서 양 변을 정리하면 `sigpa(p) = (2 ** n) * k`가 된다.
한편, `(2 ** x - 1)`과 `k`는 `p`의 약수이므로 `(2 ** x - 1) * k= sigpa(p) ≥ (2 ** x - 1) + k + k * (2 ** x - 1)`이 성립해야 한다(k > 1 인 경우). 이 식은 성립하지 않음이 자명하기에 k = 1임을 알 수 있다(k가 1일 경우 맨 끝의 `k * (2 ** x - 1)`는 사라져서 식이 성립한다). 따라서 `p = 2 ** x - 1`이 되고, `sigma(p) = 2 ** x = p + 1`이 되므로 `p`가 소수임을 보일 수 있다.

이런 생각(완전수와 메르센 소수를 엮을 생각)을 애초에 어떻게 해 내는건지는 여전히 의문이지만
의외의 계기로 짝수 완전수에 대해서도 조금 더 알게되어 약간 뿌듯!

[GIMPS]: https://ko.wikipedia.org/wiki/GIMPS
[mersenne_prime]: https://ko.wikipedia.org/wiki/%EB%A9%94%EB%A5%B4%EC%84%BC_%EC%86%8C%EC%88%98
[LLT]: https://ko.wikipedia.org/wiki/%EB%A4%BC%EC%B9%B4-%EB%A0%88%EB%A8%B8_%EC%86%8C%EC%88%98%ED%8C%90%EB%B3%84%EB%B2%95
[perfect_number]: https://ko.wikipedia.org/wiki/%EC%99%84%EC%A0%84%EC%88%98
