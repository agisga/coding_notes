## GPU and CUDA related

* Use GPU:

  ```python
  device = torch.device("cuda:0")
  torch.set_default_tensor_type('torch.cuda.FloatTensor')
  model.to(device)  # or model.to('cuda'), or model = model.cuda()
  input_batch = input_batch.to(device)  # or input_batch = input_batch.to('cuda'), or input_batch = input_batch.cuda()
  labels_batch = labels_batch.to(device)  # or labels_batch = labels_batch.to('cuda'), or labels_batch = labels_batch.cuda()
  # basically call `.to(device)` or `.cuda()` on everything
  ```

  Note that `.cuda`/`.cpu` is the old pre-0.4 way; as of 0.4 it is recommended to use `.to(device)` because it is more flexible (<https://discuss.pytorch.org/t/is-there-any-difference-between-x-to-cuda-vs-x-cuda-which-one-should-i-use/20137/2>):

  ```python
  device = torch.device('cuda:0' if torch.cuda.is_available() else 'cpu')
  x = x.to(device)
  ```

  Likewise, do `x = x.to('cpu')` and `model.to('cpu')` to work on CPU.

* Test GPU/CUDA access

  ```python
  print(torch.cuda.is_available())
  # True
  device = torch.device("cuda:0")
  print(device)
  # cuda:0
  print(torch.cuda.current_device())
  # 0
  print(torch.cuda.device(0))
  # <torch.cuda.device object at 0x7f5da13534e0>
  print(torch.cuda.device_count())
  # 1
  print(torch.cuda.get_device_name(0))
  # GeForce GTX TITAN
  print(torch.rand(2,3).cuda())
  # tensor([[0.7268, 0.1341, 0.9331],
  #         [0.8981, 0.2114, 0.1353]])
  ```
