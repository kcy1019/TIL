## Learning Rate

#### 1. Finding Learning Rate

Learning Rate를 찾는 간단하지만 효과적인 방법은 아주 작은 값부터 차근차근 늘려보며 loss의 변화를 관찰하는 것이다. 예를 들어, smoothed loss vs. learning rate 그래프를 그리고, 이를 바탕으로 loss가 줄어드는 가장 큰 lr을 선택하는 것도 한 가지 방법이다.

```python
def find_lr(model, train_dl, epochs=1, start=1e-4, end=8.0, beta=0.98):
    optimizer = optim.SGD(model.parameters(), lr=start, momentum=0.99)
    avg_loss, best_loss = 0, float('inf')
    step = (end / start) ** (1/(len(train_dl) * epochs - 1))
    i, lr = 1, start
    lr_losses = []
    for _ in range(epochs):
        for data, labels in train_dl:
            input, target = Variable(data), Variable(labels)
            model.zero_grad()
            optimizer.zero_grad()
            output = model(input)
            loss = F.nll_loss(output, target)
            avg_loss = beta * avg_loss + (1-beta) * loss.data.item()
            smoothed_loss = avg_loss / (1 - beta ** i)
            lr_losses.append((lr, smoothed_loss))
            if smoothed_loss > 4 * best_loss:
                return lr_losses
            elif smoothed_loss < best_loss:
                best_loss = smoothed_loss
            loss.backward()
            optimizer.step()
            lr *= step
            optimizer.param_groups[0]['lr'] = lr
            i += 1
    return lr_losses
```

이 함수는 모델의 lr을 start부터 end까지 변화시켜가며 smoothed loss를 계산한다. 이를 바탕으로 [mnist 데이터를 분류하는 간단한 모델][mnist-notebook]의 loss vs. lr 그래프를 그려보면 다음과 같은 모양을 볼 수 있다:

![img](https://raw.githubusercontent.com/kcy1019/TIL/images/lr-loss-graph-1.png)

이를 바탕으로 정확하지는 않지만 대략 `10 ** -1.5 `정도가 적절한 learning rate임을 확인할 수 있다.

#### References

1. [fast.ai][fastai] - Practical Deep Learning for Coders
2. [How Do You Find A Good Learning Rate][sgugger-lr]

[fastai]: https://github.com/fastai/

[sgugger-lr]: https://sgugger.github.io/how-do-you-find-a-good-learning-rate.html
[mnist-notebook]: https://github.com/kcy1019/TIL/blob/notebooks/mnist-basic.ipynb
