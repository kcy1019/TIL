# Matrix Factorization

추천 알고리즘을 찾아보면 가장 먼저 만나게 되는 방법이 바로 Matrix Factorization을 통한 Collaborative Filtering이다.
자세하게는 `User x Item (implicit) Feedback Matrix`를 `User Latent Factors, Item Latent Factors` 로 분해함으로써
아직 해당 유저가 피드백을 주지 않은 아이템에 대해서도 대해서도 `User Factor * Item Factors` 를 계산하여
피드백 값을 예측할 수 있다. (가장 유명한 예는 아무래도 영화 별점)
덧붙여서 이렇게 얻어진 Item Factor끼리의 유사도를 이용하여 유사한 (취향의) 아이템을 추천할 수도 있다.

실제로 이렇게 Factorization을 수행하는 방법에는 [Restricted Boltzman Machine][RBM], [Singular Value Decomposition][SVD], [Alternating Least Squares][ALS], [Poisson Matrix Factorization][PMF], [Neural Collaborative Filtering][neural-cf] 등등.. 정말 다양한 방법이 존재한다. 아직 다 살펴볼 엄두가 안 나서 제대로 살펴보진 못했고(TODO: 공부좀 하자), 몇 가지 얕은 기억이라도 적어둬야지..

## Plain Weighted MF (ALS) vs. Poisson MF

ALS를 이용한 MF는 모든 분포와, 분포의 파라미터 모두가 노멀을 따른다고 가정한다.
그런데 실제로 추천에 쓰이는 많은 데이터가 노멀을 따르지 않아 보이는 경우도 많고,
없는 엔트리(유저가 피드백을 주지 않은 아이템)에 대한 값도 계산이 필요해져서
이를 적당히 휴리스틱으로 잡은 값으로 때려박거나 하는 식의 단점이 존재하기에.. (물론 실제로는 엄청 잘 되지만)
노멀이 아닌 포아송, 그리고 파라미터는 감마를 따른다고 가정한 방법이 Poisson MF라고 한다.
이렇게 하면 덤으로 값이 없는 엔트리는 계산할 필요가 없어진다고 한다.


## Neural MF

~요새 트렌드는 딥러닝인데 이걸로 다 때려박으면 안 되나?~
굳이 우리가 모델을 하나하나 설정해서 돌려야 할 필요 없이,
뉴럴넷으로 매트릭스(정확히는 유저 팩터(임베딩)와 아이템 팩터)를 학습하는것.
loss function만 잘 잡아주고 레이어만 적당히 쌓아도 꽤 괜찮은 효과를 기대할 수 있는 것 같다.
인풋이 딱 떨어지는(유저, 아이템) 경우가 아니라 더 많거나 일부가 유실된 경우까지 사용할 수 있도록 확장하기도 쉽고.


[RBM]: http://www.cs.toronto.edu/~rsalakhu/papers/rbmcf.pdf
[SVD]: https://www.cs.uic.edu/~liub/KDD-cup-2007/proceedings/Regular-Paterek.pdf
[ALS]: http://yifanhu.net/PUB/cf.pdf
[PMF]: https://papers.nips.cc/paper/5360-content-based-recommendations-with-poisson-factorization.pdf
[neural-cf]: https://arxiv.org/abs/1708.05031
