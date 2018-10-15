## Learning Rate

#### 1. Finding Learning Rate

Learning Rate를 찾는 간단하지만 효과적인 방법은 아주 작은 값부터 차근차근 늘려보며 loss의 변화를 관찰하는 것이다. 예를 들어, loss vs. learning rate 그래프를 그리고, loss가 감소하는 값 중 가장 큰 것을 선택하는 방법이 있다.

```python
def find_lr(model, data_loader, num_iter):
    pass
```

#### References

1. [fast.ai][fastai] - Practical Deep Learning for Coders

[fastai]: https://github.com/fastai/

