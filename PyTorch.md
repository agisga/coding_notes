## GPU and CUDA related

* Use GPU:

  ```
  device = torch.device("cuda:0")
  torch.set_default_tensor_type('torch.cuda.FloatTensor')
  model = model.cuda()
  input_batch = input_batch.cuda()
  labels_batch = labels_batch.cuda()
  # basically call `.cuda()` on everything
  ```

* Test GPU/CUDA access

  ```
  print(torch.cuda.is_available())
  # True
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
