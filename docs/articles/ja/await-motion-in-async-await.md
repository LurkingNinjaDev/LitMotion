# async/awaitでモーションを待機する

`MotionHandle`は`GetAwaiter()`メソッドを実装しているため、そのままawaitで完了を待機することができます。

```cs
await handle;
```

`CancellationToken`を渡したい場合は以下の`ToValueTask()` / `ToAwaitable()`やUniTaskを利用してください。

## ValueTask

`MotionHandle.ToValueTask()`を使用してモーションを`ValueTask`に変換することができます。これを使用することで、async/awaitでモーションの完了を待機することが可能になります。

```cs
async ValueTask ExampleAsync(CancellationToken cancellationToken)
{
    await LMotion.Create(0f, 10f, 1f)
        .RunWithoutBinding()
        .ToValueTask(cancellationToken);
}
```

ただし、Unityにおいて`ValueTask`の使用はパフォーマンス上の問題を抱えています。最良のパフォーマンスを得るためにはUniTaskの使用を推奨します。UniTaskとの統合については[UniTask](integration-unitask.md)を参照してください。

### Awaitable

Unity 2023.1以降では、Unityで効率的なasync/awaitを実現するためのクラスである[Awaitable](https://docs.unity3d.com/2023.1/Documentation/ScriptReference/Awaitable.html)が提供されています。

Unity 2023.1以降のバージョンを使用している場合、LitMotionでは`MotionHandle`を`Awaitable`に変換する拡張メソッドとして`ToAwaitable()`が提供されます。これを使用することで、async/awaitを利用してモーションを待機することができます。

```cs
async Awaitable ExampleAsync(CancellationToken cancellationToken)
{
    await LMotion.Create(0f, 10f, 1f)
        .RunWithoutBinding()
        .ToAwaitable(cancellationToken);
}
```

### キャンセル時の挙動を変更する

`ToValueTask()` / `ToAwaitable()`の引数に`CancelBehavior`を指定することで、asyncメソッドがキャンセルされた時の挙動を変更できます。また、`cancelAwaitOnMotionCanceled`を`true`に設定することで、`MotionHandle`はキャンセルされた際にasyncメソッドをキャンセルすることが可能になります。

```cs
await LMotion.Create(0f, 10f, 1f)
    .RunWithoutBinding()
    .ToAwaitable(CancelBehavior.Complete, true, cancellationToken);
```