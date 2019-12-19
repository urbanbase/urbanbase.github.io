---
layout: post
title: "PyTorch를 활용한 딥러닝 이미지 분류 모델 학습 및 배포"
date: 2019-12-17 10:00:00
categories: dev
tags: DeepLearning AWSSagemaker PyTorch

---
 안녕하세요. 어반베이스 Machine Learning 개발자 백윤아입니다. 어반베이스에서는 공간 분석 서비스인 Space 개발을 담당하고 있습니다. Space는 컴퓨터 비전 영역의 딥러닝을 활용하여 공간을 분류하고, 공간에서 사물을 검출하며 공간의 특성을 분석하는 서비스를 제공합니다. 

 이번 글에서는 Space의 API를 만들었던 과정에 대해 이야기 해보려고 합니다. AWS 클라우드를 활용하여 간단한 딥러닝 모델 학습부터 서버리스 API 구축까지에 대한 과정인데요. 전체 내용을 두 가지 파트로 나누자면, `1. 딥러닝 모델 개발`과 `2. Serverless API 개발`으로 나눌 수 있을 것 같습니다.

 1. 딥러닝 모델 개발 : Amazon SageMaker에서 PyTorch를 활용한 딥러닝 이미지 분류 모델 학습 및 배포
 2. Serverless API 개발 : Django Rest Framework를 이용한 RESTful API 개발 & Zappa를 이용해 AWS Lambda에 Django 올리기


오늘은 딥러닝 모델 개발과정- **Amazon SageMaker에서 PyTorch를 활용한 딥러닝 이미지 분류 모델 학습 및 배포**과정에 대해 알아보겠습니다.

<br>

## Amazon SageMaker에서 PyTorch를 활용한 딥러닝 이미지 분류 모델 학습 및 배포

우선 간단한 이미지 분류기를 만들기 위해서 학습에 사용할 데이터가 필요합니다. 

이번 튜토리얼에서는 최근 핫(?)한 딥러닝 프레임워크인 PyTorch를 사용하여 모델 학습을 해보려고 하는데, PyTorch에는 Computer Vision 분야를 위한 <a href="https://pytorch.org/docs/stable/torchvision/index.html#torchvision" target="_blank" style="color: #0366d6;">torchvision</a>이라는 패키지가 만들어져 있습니다. 

여기에는 ImageNet이나 CIFAR10, MNIST 등과 같이 널리 사용되는 유명한 데이터셋(datasets)과 모델 아키텍처(models) 및 이미지용 데이터 변환기(transforms)가 포함되어 있습니다. 이 패키지를 사용하면 편리하게 대량의 이미지 데이터를 불러오고, 데이터 변환도 손쉽게 할 수 있습니다. 여기서는 **나만의 데이터셋**으로 세 종류의 고양이를 분류하는 모델을 만들기 위해서 `torchvision`의 `ImageFolder` 모듈을 사용합니다.
<br>

## <사전환경설정>

### 1. Custom Dataset 구성하기

데이터셋의 구성은 아래와 같습니다. root 디렉토리를 하나 생성하고, train/validation 디렉토리로 데이터를 나누어줍니다. 학습하고자하는 데이터셋의 레이블(Label)을 그 아래 디렉토리 이름으로 지정하고, 레이블 이름의 디렉토리 안에 그 레이블에 해당되는 이미지를 준비합니다. train data는 레이블당 각각 800장 내외, validation data는 레이블당 각각 200장 내외로 준비했습니다. 

```bash
cat_data
    ├── train
    │ 	  ├── chartreux
    │     │       ├── chartreux_1.jpg
    │     │       ├── chartreux_2.jpg
    │     │       ├── chartreux_3.jpg
    │     │       └── ...
    │ 	  ├── persian
    │     └── ragdoll
    └── validation
            ├── chartreux
            │       ├── chartreux_1.jpg
            │       ├── chartreux_2.jpg
            │       ├── chartreux_3.jpg
            │       └── ...
            ├── persian
            └── ragdoll
```
<br>

### 2. Amazon SageMaker 노트북 환경 설정하기

 이제 딥러닝 모델 학습을 시작하기 위해 노트북 인스턴스를 만들어 보겠습니다. AWS Management Console에 로그인하고 Amazon SageMaker 서비스를 선택한 다음 콘솔 대시 보드에서 **노트북 인스턴스 생성**을 선택하여 다음 페이지를 엽니다.

<img src="/assets/12_DL image classification/create_notebook_instance.jpg" alt="create_notebook_instance">


노트북 인스턴스의 이름을 정하고 원하는 인스턴스 유형을 선택한 후, `Create notebook instance` 버튼을 누르면 새로운 노트북 인스턴스가 생성됩니다. 

화면 상단에 있는 `New` 버튼을 누르고 아래와 같이 `conda_pytorch_p36` 을 선택하여 새로운 노트북을 생성합니다. Conda 패키지를 이용하여 Python3 PyTorch 환경이 설정되어 있는 노트북입니다. 

<img src="/assets/12_DL image classification/create_notebook.jpg" alt="create_notebook">


노트북에서 Python과 PyTorch의 버전을 확인해보겠습니다.

```python
from platform import python_version
import torch

print("Python", python_version())
print("PyTorch", torch.__version__)
```
```bash
Python 3.6.5
PyTorch 1.2.0
```

잘 설치 되어있네요!

그럼 SageMaker Session, 데이터 및 모델을 저장할 Amazon S3의 bucket과 prefix(bucket 내의 folder 이름), IAM role을 설정하겠습니다.  

```python
import sagemaker
# Session
sagemaker_session = sagemaker.Session()
# Amazon S3
bucket = sagemaker_session.default_bucket()
prefix = 'DEMO-pytorch-cat-classifier'
# IAM role
role = sagemaker.get_execution_role()
```
<br>

### 3. Data 불러오기

이제 데이터를 학습에 사용할 수 있도록 간단한 전처리를 하고 불러오도록 하겠습니다. 우선 앞에서 구성했던 데이터셋 디렉토리(cat_data)를 터미널을 사용해서 노트북과 같은 경로에 업로드 합니다. 앞서 언급한 것처럼 이미지 데이터를 불러오기 위해서 `torchvision`의 `ImageFolder`를 사용하고 <a href="https://pytorch.org/docs/stable/torchvision/transforms.html?highlight=transform" target="_blank" style="color: #0366d6;"> transforms</a>으로 여러 가지 이미지 전처리를 할 수 있습니다. 여기서는 이미지 크기를 일정하게 바꿔서 tensor로 변환해주고 정규화시켜주는 기능만 사용했습니다. 

```python
import os
import torch
import torch.utils.data
from torchvision import datasets, transforms

root_dir = 'cat_data'

transforms = transforms.Compose([
    transforms.Resize((96, 96)),
    transforms.ToTensor(),
    transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))
])

trainset = datasets.ImageFolder(os.path.join(root_dir, 'train'), transform=transforms)
testset = datasets.ImageFolder(os.path.join(root_dir, 'validation'), transform=transforms)

train_loader = torch.utils.data.DataLoader(trainset, batch_size=4, shuffle=True)
test_loader = torch.utils.data.DataLoader(testset, batch_size=4, shuffle=True)
``` 

`DataLoader`를 사용해서 주어진 데이터셋을 지정한 batch_size 만큼 iterable하게 불러올 수 있습니다.
데이터가 잘 로드되었나 확인하기 위해서 학습 데이터셋을 무작위로 불러와 보겠습니다.

```python
import numpy as np
import torchvision, torch
import matplotlib.pyplot as plt

# labels list
classes = trainset.classes

# function to show an image
def imshow(img):
    img = img / 2 + 0.5     # unnormalize
    npimg = img.numpy()
    plt.figure(figsize=(10, 10))
    plt.axis("off")
    plt.imshow(np.transpose(npimg, (1, 2, 0)))

# get some random training images
dataiter = iter(train_loader)
images, labels = dataiter.next()

# show images
imshow(torchvision.utils.make_grid(images))

# print labels
print('     ', '        '.join('%9s' % classes[labels[j]] for j in range(4)))
```

<img src="/assets/12_DL image classification/trainset.png" alt="trainset">

<br>

### 4. Data를 Amazon S3에 업로드하기

`sagemaker.Session.upload_data` 함수를 사용하여 데이터셋을 Amazon S3에 업로드합니다. 함수의 반환 값은 데이터셋이 저장된 S3 경로입니다.

```python
inputs = sagemaker_session.upload_data(path=root_dir, bucket=bucket, key_prefix=prefix+'/data')
```
<br>
<br>

## <학습하기>
### 1. Training Script 작성하기

모델을 학습시키기 위해서는 PyTorch 프레임워크를 사용하여 작성된 스크립트가 필요합니다. 스크립트에는 모델을 training하고 hosting하는 데 필요한 모든 코드가 있어야 합니다. 이 스크립트는 SageMaker 외부에서 실행할 수 있는 training 스크립트와 유사하지만, 아래와 같은 다양한 환경변수를 추가하여 학습 환경 속성에 액세스할 수 있도록 해주어야 합니다. <a href="https://sagemaker.readthedocs.io/en/stable/using_pytorch.html" target="_blank" style="color: #0366d6;"> Using PyTorch with the SageMaker Python SDK</a>에서 더 자세한 내용을 확인할 수 있습니다.

- SM_HOSTS: 모든 host를 포함하는 JSON encoded list
- SM_CURRENT_HOST: 현재 컨테이너의 이름
- SM_MODEL_DIR: 모델 아티팩트를 저장할 디렉토리 경로를 나타내는 문자열
- SM_NUM_GPUS: 현재 컨테이너에서 사용 가능한 gpu 수
- SM_CHANNEL_TRAINING: 'training' 채널에 데이터가 저장된 디렉토리의 경로를 나타내는 문자열

아래 코드는 이번 튜토리얼에서 사용한 training script 전체 코드입니다. 고양이의 품종을 분류하기 위해서 간단한 CNN(Convolution Neural Network)을 정의했습니다.

<details>
<summary><a style="color: #0366d6;">전체 코드 펼치기</a></summary>
<div markdown="1">

```python
import argparse
import json
import logging
import os
import sagemaker_containers
import sys
import torch
import torch.distributed as dist
import torch.nn as nn
import torch.nn.functional as F
import torch.optim as optim
import torch.utils.data
import torch.utils.data.distributed
from torchvision import datasets, transforms

logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler(sys.stdout))


class Net(nn.Module):
    def __init__(self):
        super(Net, self).__init__()
        self.conv1 = nn.Conv2d(3, 6, 7)
        self.conv2 = nn.Conv2d(6, 16, 3)
        self.conv3 = nn.Conv2d(16, 64, 3)
        self.fc1 = nn.Linear(64 * 9 * 9, 500)
        self.fc2 = nn.Linear(500, 250)
        self.fc3 = nn.Linear(250, 120)
        self.fc4 = nn.Linear(120, 84)
        self.fc5 = nn.Linear(84, 3)
        
        self.pool = nn.MaxPool2d(2, 2)
        self.dropout = nn.Dropout(.2)
        self.final_act = nn.LogSoftmax(dim=-1)

    def forward(self, x):
        x = self.pool(F.relu(self.conv1(x)))
        x = self.pool(F.relu(self.conv2(x)))
        x = self.pool(F.relu(self.conv3(x)))
        x = x.view(-1, 64 * 9 * 9)
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = F.relu(self.fc3(x))
        x = F.relu(self.fc4(x))
        x = self.final_act(self.fc5(x))
        return x

def average_gradients(model):
    # Gradient averaging.
    size = float(dist.get_world_size())
    for param in model.parameters():
        dist.all_reduce(param.grad.data, op=dist.reduce_op.SUM)
        param.grad.data /= size


def train(args):
    is_distributed = len(args.hosts) > 1 and args.backend is not None
    logger.debug("Distributed training - {}".format(is_distributed))
    use_cuda = args.num_gpus > 0
    logger.debug("Number of gpus available - {}".format(args.num_gpus))
    kwargs = {'num_workers': 1, 'pin_memory': True} if use_cuda else {}
    device = torch.device("cuda" if use_cuda else "cpu")

    if is_distributed:
        # Initialize the distributed environment.
        world_size = len(args.hosts)
        os.environ['WORLD_SIZE'] = str(world_size)
        host_rank = args.hosts.index(args.current_host)
        os.environ['RANK'] = str(host_rank)
        dist.init_process_group(backend=args.backend, rank=host_rank, world_size=world_size)
        logger.info('Initialized the distributed environment: \'{}\' backend on {} nodes. '.format(
            args.backend, dist.get_world_size()) + 'Current host rank is {}. Number of gpus: {}'.format(
            dist.get_rank(), args.num_gpus))

    # set the seed for generating random numbers
    torch.manual_seed(args.seed)
    if use_cuda:
        torch.cuda.manual_seed(args.seed)
    
    # data loader
    logger.info("Loading Cat dataset")
    transform = transforms.Compose([
        transforms.Resize((96, 96)),
        transforms.ToTensor(),
        transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))
    ])
    trainset = datasets.ImageFolder(os.path.join(args.data_dir, 'train'), transform=transform)
    testset = datasets.ImageFolder(os.path.join(args.data_dir, 'validation'), transform=transform)
    
    train_sampler = torch.utils.data.distributed.DistributedSampler(trainset) if is_distributed else None
    
    train_loader = torch.utils.data.DataLoader(trainset, args.batch_size, shuffle=train_sampler is None, sampler=train_sampler)
    test_loader = torch.utils.data.DataLoader(testset, args.test_batch_size, shuffle=True)
    
    # model 
    logger.info("Model loaded")
    model = Net().to(device)
    if is_distributed and use_cuda:
        # multi-machine multi-gpu case
        model = torch.nn.parallel.DistributedDataParallel(model)
    else:
        # single-machine multi-gpu case or single-machine or multi-machine cpu case
        model = torch.nn.DataParallel(model)
    
    criterion = nn.NLLLoss().to(device)
    optimizer = optim.SGD(model.parameters(), lr=args.lr, momentum=args.momentum)

    for epoch in range(1, args.epochs + 1):
        model.train()
        for batch_idx, (data, target) in enumerate(train_loader, 1):
            data, target = data.to(device), target.to(device)
            optimizer.zero_grad()
            output = model(data)
            loss = criterion(output, target)
            loss.backward()
            if is_distributed and not use_cuda:
                # average gradients manually for multi-machine cpu case only
                average_gradients(model)
            optimizer.step()
            if batch_idx % args.log_interval == 0:
                logger.info('Train Epoch: {} [{}/{} ({:.0f}%)] Loss: {:.6f}'.format(
                    epoch, batch_idx * len(data), len(train_loader.sampler),
                    100. * batch_idx / len(train_loader), loss.item()))
        test(model, test_loader, device)   
    save_model(model, args.model_dir)


def test(model, test_loader, device):
    model.eval()
    criterion = nn.NLLLoss().to(device)
    test_loss = 0
    correct = 0
    with torch.no_grad():
        for data, target in test_loader:
            data, target = data.to(device), target.to(device)
            output = model(data)
            loss = criterion(output, target)
            test_loss += loss.item()  # sum up batch loss
            pred = output.max(1, keepdim=True)[1]  # get the index of the max log-probability
            correct += pred.eq(target.view_as(pred)).sum().item()

    test_loss /= len(test_loader.dataset)
    logger.info('Test set: Average loss: {:.4f}, Accuracy: {}/{} ({:.0f}%)\n'.format(
        test_loss, correct, len(test_loader.dataset),
        100. * correct / len(test_loader.dataset)))

    
def save_model(model, model_dir):
    logger.info("Saving the model.")
    path = os.path.join(model_dir, 'model.pth')
    torch.save(model.cpu().state_dict(), path)
    
    
def model_fn(model_dir):
    device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
    model = torch.nn.DataParallel(Net())
    with open(os.path.join(model_dir, 'model.pth'), 'rb') as f:
        model.load_state_dict(torch.load(f))
    return model.to(device)



if __name__ == '__main__':
    parser = argparse.ArgumentParser()

    # Data and model checkpoints directories
    parser.add_argument('--batch-size', type=int, default=4, metavar='N',
                        help='input batch size for training (default: 4)')
    parser.add_argument('--test-batch-size', type=int, default=4, metavar='N',
                        help='input batch size for testing (default: 4)')
    parser.add_argument('--epochs', type=int, default=10, metavar='N',
                        help='number of epochs to train (default: 10)')
    parser.add_argument('--lr', type=float, default=0.001, metavar='LR',
                        help='learning rate (default: 0.001)')
    parser.add_argument('--momentum', type=float, default=0.9, metavar='M',
                        help='SGD momentum (default: 0.9)')
    parser.add_argument('--seed', type=int, default=1, metavar='S',
                        help='random seed (default: 1)')
    parser.add_argument('--log-interval', type=int, default=100, metavar='N',
                        help='how many batches to wait before logging training status')
    parser.add_argument('--backend', type=str, default=None,
                        help='backend for distributed training (tcp, gloo on cpu and gloo, nccl on gpu)')

    # Container environment
    parser.add_argument('--hosts', type=list, default=json.loads(os.environ['SM_HOSTS']))
    parser.add_argument('--current-host', type=str, default=os.environ['SM_CURRENT_HOST'])
    parser.add_argument('--model-dir', type=str, default=os.environ['SM_MODEL_DIR'])
    parser.add_argument('--data-dir', type=str, default=os.environ['SM_CHANNEL_TRAINING'])
    parser.add_argument('--num-gpus', type=int, default=os.environ['SM_NUM_GPUS'])

    train(parser.parse_args())
```

</div>
</details>


training script를 다음과 같이 크게 4가지 부분으로 나눌 수 있습니다. 

#### 1) model network 부분

```python
class Net(nn.Module):
    def __init__(self):
        super(Net, self).__init__()
        self.conv1 = nn.Conv2d(3, 6, 7)
        self.conv2 = nn.Conv2d(6, 16, 3)
        self.conv3 = nn.Conv2d(16, 64, 3)
        self.fc1 = nn.Linear(64 * 9 * 9, 500)
        self.fc2 = nn.Linear(500, 250)
        self.fc3 = nn.Linear(250, 120)
        self.fc4 = nn.Linear(120, 84)
        self.fc5 = nn.Linear(84, 3)
        
        self.pool = nn.MaxPool2d(2, 2)
        self.dropout = nn.Dropout(.2)
        self.final_act = nn.LogSoftmax(dim=-1)

    def forward(self, x):
        x = self.pool(F.relu(self.conv1(x)))
        x = self.pool(F.relu(self.conv2(x)))
        x = self.pool(F.relu(self.conv3(x)))
        x = x.view(-1, 64 * 9 * 9)
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = F.relu(self.fc3(x))
        x = F.relu(self.fc4(x))
        x = self.final_act(self.fc5(x))
        return x
```

#### 2) training 실행(data loader 포함) 부분
```python
def train(args):
    is_distributed = len(args.hosts) > 1 and args.backend is not None
    logger.debug("Distributed training - {}".format(is_distributed))
    use_cuda = args.num_gpus > 0
    logger.debug("Number of gpus available - {}".format(args.num_gpus))
    kwargs = {'num_workers': 1, 'pin_memory': True} if use_cuda else {}
    device = torch.device("cuda" if use_cuda else "cpu")

    if is_distributed:
        # Initialize the distributed environment.
        world_size = len(args.hosts)
        os.environ['WORLD_SIZE'] = str(world_size)
        host_rank = args.hosts.index(args.current_host)
        os.environ['RANK'] = str(host_rank)
        dist.init_process_group(backend=args.backend, rank=host_rank, world_size=world_size)
        logger.info('Initialized the distributed environment: \'{}\' backend on {} nodes. '.format(
            args.backend, dist.get_world_size()) + 'Current host rank is {}. Number of gpus: {}'.format(
            dist.get_rank(), args.num_gpus))

    # set the seed for generating random numbers
    torch.manual_seed(args.seed)
    if use_cuda:
        torch.cuda.manual_seed(args.seed)
    
    # data loader
    logger.info("Loading Cat dataset")
    transform = transforms.Compose([
        transforms.Resize((96, 96)),
        transforms.ToTensor(),
        transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))
    ])
    trainset = datasets.ImageFolder(os.path.join(args.data_dir, 'train'), transform=transform)
    testset = datasets.ImageFolder(os.path.join(args.data_dir, 'validation'), transform=transform)
    
    train_sampler = torch.utils.data.distributed.DistributedSampler(trainset) if is_distributed else None
    
    train_loader = torch.utils.data.DataLoader(trainset, args.batch_size, shuffle=train_sampler is None, sampler=train_sampler)
    test_loader = torch.utils.data.DataLoader(testset, args.test_batch_size, shuffle=True)
    
    # model 
    logger.info("Model loaded")
    model = Net().to(device)
    if is_distributed and use_cuda:
        # multi-machine multi-gpu case
        model = torch.nn.parallel.DistributedDataParallel(model)
    else:
        # single-machine multi-gpu case or single-machine or multi-machine cpu case
        model = torch.nn.DataParallel(model)
    
    # loss function and optimizer
    criterion = nn.NLLLoss().to(device)
    optimizer = optim.SGD(model.parameters(), lr=args.lr, momentum=args.momentum)

    for epoch in range(1, args.epochs + 1):
        model.train()
        for batch_idx, (data, target) in enumerate(train_loader, 1):
            data, target = data.to(device), target.to(device)
            optimizer.zero_grad()
            output = model(data)
            loss = criterion(output, target)
            loss.backward()
            if is_distributed and not use_cuda:
                # average gradients manually for multi-machine cpu case only
                average_gradients(model)
            optimizer.step()
            if batch_idx % args.log_interval == 0:
                logger.info('Train Epoch: {} [{}/{} ({:.0f}%)] Loss: {:.6f}'.format(
                    epoch, batch_idx * len(data), len(train_loader.sampler),
                    100. * batch_idx / len(train_loader), loss.item()))
        test(model, test_loader, device)   
    save_model(model, args.model_dir)
```
#### 3) model 저장/로드 부분

```python
def save_model(model, model_dir):
    logger.info("Saving the model.")
    path = os.path.join(model_dir, 'model.pth')
    torch.save(model.cpu().state_dict(), path)
    
    
def model_fn(model_dir):
    device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
    model = torch.nn.DataParallel(Net())
    with open(os.path.join(model_dir, 'model.pth'), 'rb') as f:
        model.load_state_dict(torch.load(f))
    return model.to(device)
```
#### 4) 환경변수 및 학습 파라미터 설정 부분

```python
if __name__ == '__main__':
    parser = argparse.ArgumentParser()

    # Training parameters
    parser.add_argument('--batch-size', type=int, default=4, metavar='N',
                        help='input batch size for training (default: 4)')
    parser.add_argument('--test-batch-size', type=int, default=4, metavar='N',
                        help='input batch size for testing (default: 4)')
    parser.add_argument('--epochs', type=int, default=10, metavar='N',
                        help='number of epochs to train (default: 10)')
    parser.add_argument('--lr', type=float, default=0.001, metavar='LR',
                        help='learning rate (default: 0.001)')
    parser.add_argument('--momentum', type=float, default=0.9, metavar='M',
                        help='SGD momentum (default: 0.9)')
    parser.add_argument('--seed', type=int, default=1, metavar='S',
                        help='random seed (default: 1)')
    parser.add_argument('--log-interval', type=int, default=100, metavar='N',
                        help='how many batches to wait before logging training status')
    parser.add_argument('--backend', type=str, default=None,
                        help='backend for distributed training (tcp, gloo on cpu and gloo, nccl on gpu)')

    # Container environment
    parser.add_argument('--hosts', type=list, default=json.loads(os.environ['SM_HOSTS']))
    parser.add_argument('--current-host', type=str, default=os.environ['SM_CURRENT_HOST'])
    parser.add_argument('--model-dir', type=str, default=os.environ['SM_MODEL_DIR'])
    parser.add_argument('--data-dir', type=str, default=os.environ['SM_CHANNEL_TRAINING'])
    parser.add_argument('--num-gpus', type=int, default=os.environ['SM_NUM_GPUS'])
```
<br>

### 2. PyTorch Estimator 생성하기 (모델 학습)

자! 이제 드디어 학습을 시작할 수 있는 준비가 끝났습니다~~
SageMaker에서 Pytorch 프레임워크를 이용하여 모델을 학습시키기 위해 모델의 학습부터 배포까지 end-to-end로 처리해주는 <a href="https://sagemaker.readthedocs.io/en/stable/sagemaker.pytorch.html" target="_blank" style="color: #0366d6;"> PyTorch Estimator</a>를 생성해줍니다.

```python
from sagemaker.pytorch import PyTorch

estimator = PyTorch(entry_point='classifier.py',
                    role=role,
                    framework_version='1.2.0',
                    train_instance_count=1,
                    train_instance_type='ml.p2.8xlarge',
                    hyperparameters={
                        'epochs': 30,
                        'backend': 'gloo'
                    })
```
여기서 사용한 estimator의 기본적인 parameters를 살펴보도록 하겠습니다.

- entry_point : training script 경로를 나타내는 문자열
- role : 초반에 설정했던 IAM role
- framework_version : 모델 학습 코드를 실행하는 데 사용하는 PyTorch 버전
- train_instance_count : 학습에 사용될 인스턴스의 개수
- train_instance_type : 학습에 사용될 인스턴스 유형
- hyperparameters : 학습에 사용될 하이퍼파라미터. training script에 argument로 설정한 파라미터들

이번 분류 모델 학습에서는 'ml.p2.8xlarge' 인스턴스를 1개 사용하여 30 epochs을 설정 해주었고, 나머지 파라미터들은 training script에서 설정해준 기본값을 사용했습니다. 이제 학습을 시작해보도록 하겠습니다.

```python
estimator.fit({'training': inputs})  # inputs : dataset이 저장된 S3 경로
```

```bash
2019-12-17 07:12:40 Starting - Starting the training job...
2019-12-17 07:12:42 Starting - Launching requested ML instances......
2019-12-17 07:14:10 Starting - Preparing the instances for training.........
2019-12-17 07:15:18 Downloading - Downloading input data...
2019-12-17 07:16:11 Training - Downloading the training image........bash: cannot set terminal process group (-1): Inappropriate ioctl for device
bash: no job control in this shell
2019-12-17 07:17:23,431 sagemaker-containers INFO     Imported framework sagemaker_pytorch_container.training
2019-12-17 07:17:23,511 sagemaker_pytorch_container.training INFO     Block until all host DNS lookups succeed.
2019-12-17 07:17:26,577 sagemaker_pytorch_container.training INFO     Invoking user training script.
2019-12-17 07:17:26,827 sagemaker-containers INFO     Module classifier does not provide a setup.py. 
Generating setup.py
2019-12-17 07:17:26,827 sagemaker-containers INFO     Generating setup.cfg
2019-12-17 07:17:26,828 sagemaker-containers INFO     Generating MANIFEST.in
2019-12-17 07:17:26,828 sagemaker-containers INFO     Installing module with the following command:
/opt/conda/bin/python -m pip install . 
Processing /opt/ml/code
Building wheels for collected packages: classifier
  Building wheel for classifier (setup.py): started
  Building wheel for classifier (setup.py): finished with status 'done'
  Created wheel for classifier: filename=classifier-1.0.0-py2.py3-none-any.whl size=9670 sha256=238042f454d144dadbf700d338582f45f0074e9ba049cad42834e13f762f1bee
  Stored in directory: /tmp/pip-ephem-wheel-cache-wtxhriws/wheels/35/24/16/37574d11bf9bde50616c67372a334f94fa8356bc7164af8ca3
Successfully built classifier
Installing collected packages: classifier
Successfully installed classifier-1.0.0
WARNING: You are using pip version 19.3; however, version 19.3.1 is available.
You should consider upgrading via the 'pip install --upgrade pip' command.
2019-12-17 07:17:28,975 sagemaker-containers INFO     Invoking user script

Training Env:

{
    "additional_framework_parameters": {},
    "channel_input_dirs": {
        "training": "/opt/ml/input/data/training"
    },
    "current_host": "algo-1",
    "framework_module": "sagemaker_pytorch_container.training:main",
    "hosts": [
        "algo-1"
    ],
    "hyperparameters": {
        "backend": "gloo",
        "epochs": 30
    },
    "input_config_dir": "/opt/ml/input/config",
    "input_data_config": {
        "training": {
            "TrainingInputMode": "File",
            "S3DistributionType": "FullyReplicated",
            "RecordWrapperType": "None"
        }
    },
    "input_dir": "/opt/ml/input",
    "is_master": true,
    "job_name": "pytorch-training-2019-12-17-07-12-39-842",
    "log_level": 20,
    "master_hostname": "algo-1",
    "model_dir": "/opt/ml/model",
    "module_dir": "s3://sagemaker-ap-northeast-2-444682503693/pytorch-training-2019-12-17-07-12-39-842/source/sourcedir.tar.gz",
    "module_name": "classifier",
    "network_interface_name": "eth0",
    "num_cpus": 32,
    "num_gpus": 8,
    "output_data_dir": "/opt/ml/output/data",
    "output_dir": "/opt/ml/output",
    "output_intermediate_dir": "/opt/ml/output/intermediate",
    "resource_config": {
        "current_host": "algo-1",
        "hosts": [
            "algo-1"
        ],
        "network_interface_name": "eth0"
    },
    "user_entry_point": "classifier.py"
}

Environment variables:

SM_HOSTS=["algo-1"]
SM_NETWORK_INTERFACE_NAME=eth0
SM_HPS={"backend":"gloo","epochs":30}
SM_USER_ENTRY_POINT=classifier.py
SM_FRAMEWORK_PARAMS={}
SM_RESOURCE_CONFIG={"current_host":"algo-1","hosts":["algo-1"],"network_interface_name":"eth0"}
SM_INPUT_DATA_CONFIG={"training":{"RecordWrapperType":"None","S3DistributionType":"FullyReplicated","TrainingInputMode":"File"}}
SM_OUTPUT_DATA_DIR=/opt/ml/output/data
SM_CHANNELS=["training"]
SM_CURRENT_HOST=algo-1
SM_MODULE_NAME=classifier
SM_LOG_LEVEL=20
SM_FRAMEWORK_MODULE=sagemaker_pytorch_container.training:main
SM_INPUT_DIR=/opt/ml/input
SM_INPUT_CONFIG_DIR=/opt/ml/input/config
SM_OUTPUT_DIR=/opt/ml/output
SM_NUM_CPUS=32
SM_NUM_GPUS=8
SM_MODEL_DIR=/opt/ml/model
SM_MODULE_DIR=s3://sagemaker-ap-northeast-2-444682503693/pytorch-training-2019-12-17-07-12-39-842/source/sourcedir.tar.gz
SM_TRAINING_ENV={"additional_framework_parameters":{},"channel_input_dirs":{"training":"/opt/ml/input/data/training"},"current_host":"algo-1","framework_module":"sagemaker_pytorch_container.training:main","hosts":["algo-1"],"hyperparameters":{"backend":"gloo","epochs":30},"input_config_dir":"/opt/ml/input/config","input_data_config":{"training":{"RecordWrapperType":"None","S3DistributionType":"FullyReplicated","TrainingInputMode":"File"}},"input_dir":"/opt/ml/input","is_master":true,"job_name":"pytorch-training-2019-12-17-07-12-39-842","log_level":20,"master_hostname":"algo-1","model_dir":"/opt/ml/model","module_dir":"s3://sagemaker-ap-northeast-2-444682503693/pytorch-training-2019-12-17-07-12-39-842/source/sourcedir.tar.gz","module_name":"classifier","network_interface_name":"eth0","num_cpus":32,"num_gpus":8,"output_data_dir":"/opt/ml/output/data","output_dir":"/opt/ml/output","output_intermediate_dir":"/opt/ml/output/intermediate","resource_config":{"current_host":"algo-1","hosts":["algo-1"],"network_interface_name":"eth0"},"user_entry_point":"classifier.py"}
SM_USER_ARGS=["--backend","gloo","--epochs","30"]
SM_OUTPUT_INTERMEDIATE_DIR=/opt/ml/output/intermediate
SM_CHANNEL_TRAINING=/opt/ml/input/data/training
SM_HP_BACKEND=gloo
SM_HP_EPOCHS=30
PYTHONPATH=/opt/conda/bin:/opt/conda/lib/python36.zip:/opt/conda/lib/python3.6:/opt/conda/lib/python3.6/lib-dynload:/opt/conda/lib/python3.6/site-packages

Invoking script with the following command:

/opt/conda/bin/python -m classifier --backend gloo --epochs 30



2019-12-17 07:17:21 Training - Training image download completed. Training in progress.Distributed training - False
Number of gpus available - 8
Get train data loader
Get test data loader
Processes 2399/2399 (100%) of train data
Processes 597/597 (100%) of test data
NCCL version 2.4.8+cuda10.0
Train Epoch: 1 [400/2399 (17%)] Loss: 1.111847
Train Epoch: 1 [800/2399 (33%)] Loss: 1.074032
Train Epoch: 1 [1200/2399 (50%)] Loss: 1.168240
Train Epoch: 1 [1600/2399 (67%)] Loss: 0.983421
Train Epoch: 1 [2000/2399 (83%)] Loss: 1.053726
Train Epoch: 1 [1800/2399 (100%)] Loss: 1.011850
Test set: Average loss: 1.0778, Accuracy: 240/597 (40%)

Train Epoch: 2 [400/2399 (17%)] Loss: 1.025982
Train Epoch: 2 [800/2399 (33%)] Loss: 0.859558
Train Epoch: 2 [1200/2399 (50%)] Loss: 0.866786
Train Epoch: 2 [1600/2399 (67%)] Loss: 0.679422
Train Epoch: 2 [2000/2399 (83%)] Loss: 0.887831
Train Epoch: 2 [1800/2399 (100%)] Loss: 0.989252
Test set: Average loss: 0.9589, Accuracy: 309/597 (52%)

.
.
.

Train Epoch: 30 [400/2399 (17%)] Loss: 0.000451
Train Epoch: 30 [800/2399 (33%)] Loss: 0.016253
Train Epoch: 30 [1200/2399 (50%)] Loss: 0.095858
Train Epoch: 30 [1600/2399 (67%)] Loss: 0.035938
Train Epoch: 30 [2000/2399 (83%)] Loss: 0.009349
Train Epoch: 30 [1800/2399 (100%)] Loss: 0.009735
Test set: Average loss: 1.0286, Accuracy: 447/597 (75%)

Saving the model.
2019-12-17 08:13:55,151 sagemaker-containers INFO     Reporting training SUCCESS

2019-12-17 08:14:05 Uploading - Uploading generated training model
2019-12-17 08:14:05 Completed - Training job completed
Training seconds: 3527
Billable seconds: 3527
```

마지막 epoch의 모델 정확도가 약 75%로 기록이 되었네요~

<br>
<br>

## <평가하기>
### 1. PyTorch Predictor 배포하기 (Endpoint 생성)
학습이 끝나면 PyTorch Estimator의 object를 사용하여 PyTorch Predictor를 빌드하고 배포합니다. 그러면 추론을 수행하는 데 사용할 수 있는 호스팅된 endpoint가 생성됩니다. 학습할 때와 마찬가지로 추론에 사용될 인스턴스의 수와 유형을 설정할 수 있습니다.

```python
predictor = estimator.deploy(initial_instance_count=1, instance_type='ml.m4.xlarge')
```
<Br>

### 2. Test dataset 으로 모델 평가하기
Endpoint가 생성이 되었으면 모델을 test dataset으로 학습이 잘 되었는지 평가해보겠습니다. 모델이 예측한 output과 진짜 정답(Ground-truth)을 비교하는 방식으로 확인합니다.

```python
# get some test images
dataiter = iter(test_loader)
images, labels = dataiter.next()

# print images
imshow(torchvision.utils.make_grid(images))
print('GroundTruth:', '     '.join('%4s' % classes[labels[j]] for j in range(4)))

# model prediction
outputs = predictor.predict(images.numpy())

_, predicted = torch.max(torch.from_numpy(np.array(outputs)), 1)

print('Prediction: ', '     '.join('%4s' % classes[predicted[j]] for j in range(4)))
```
<img src="/assets/12_DL image classification/inference.png" alt="inference">

잘 맞춘것도 있고, 그렇지 않은것도 있습니다. 아무래도 학습 데이터셋의 양이 2400장 정도로 매우 적기도 하고, 모델 신경망의 layer도 깊지가 않아서 학습이 어려웠나봅니다. 이런 상황에서 모델의 정확도를 높이기 위해 **전이 학습(Transfer Learning)** 을 이용할 수 있습니다!

<br>

### 3. Transfer Learning 

여기서는 전이 학습 시나리오 중에서 대용량의 데이터셋으로 사전 학습된 모델을 로드하여 final fully connected layer만 나만의 데이터셋에 맞게 재설정하는 **Fine Tuning** 방식을 사용하겠습니다. 사전 훈련된 모델은 `torchvision`의 <a href="https://pytorch.org/docs/stable/torchvision/models.html#classification" target="_blank" style="color: #0366d6;">models</a> 패키지에서 제공하는 `resnet101` 모델을 사용했습니다. 
위에서 사용했던 training script에서 model을 불러오는 부분과 손실함수 부분만 아래와 같이 바꿔준 뒤에 나머지 코드는 그대로 두고 똑같이 학습시키기만 하면 됩니다.

```python
# pretrained model loaded
model = models.resnet101(pretrained=True)
num_ftrs = model.fc.in_features
model.fc = nn.Linear(num_ftrs, 3)
model = model.to(device)

# loss function
criterion = nn.CrossEntropyLoss().to(device)
```

```bash
Train Epoch: 1 [400/2399 (17%)] Loss: 1.562547
Train Epoch: 1 [800/2399 (33%)] Loss: 3.192161
Train Epoch: 1 [1200/2399 (50%)] Loss: 0.412022
Train Epoch: 1 [1600/2399 (67%)] Loss: 0.915290
Train Epoch: 1 [2000/2399 (83%)] Loss: 1.298079
Train Epoch: 1 [1800/2399 (100%)] Loss: 1.683719
Test set: Average loss: 0.1399, Accuracy: 460/597 (77%)

Train Epoch: 2 [400/2399 (17%)] Loss: 0.949771
Train Epoch: 2 [800/2399 (33%)] Loss: 0.222953
Train Epoch: 2 [1200/2399 (50%)] Loss: 0.070903
Train Epoch: 2 [1600/2399 (67%)] Loss: 0.313359
Train Epoch: 2 [2000/2399 (83%)] Loss: 0.872189
Train Epoch: 2 [1800/2399 (100%)] Loss: 0.908455
Test set: Average loss: 0.1076, Accuracy: 512/597 (86%)

Train Epoch: 3 [400/2399 (17%)] Loss: 0.076400
Train Epoch: 3 [800/2399 (33%)] Loss: 1.142258
Train Epoch: 3 [1200/2399 (50%)] Loss: 0.511040
Train Epoch: 3 [1600/2399 (67%)] Loss: 0.163432
Train Epoch: 3 [2000/2399 (83%)] Loss: 0.191089
Train Epoch: 3 [1800/2399 (100%)] Loss: 0.380600
Test set: Average loss: 0.1487, Accuracy: 436/597 (73%)

.
.
.

Train Epoch: 19 [400/2399 (17%)] Loss: 0.013524
Train Epoch: 19 [800/2399 (33%)] Loss: 0.015956
Train Epoch: 19 [1200/2399 (50%)] Loss: 0.000095
Train Epoch: 19 [1600/2399 (67%)] Loss: 0.000208
Train Epoch: 19 [2000/2399 (83%)] Loss: 0.003590
Train Epoch: 19 [1800/2399 (100%)] Loss: 0.000108
Test set: Average loss: 0.1088, Accuracy: 532/597 (89%)

Train Epoch: 20 [400/2399 (17%)] Loss: 0.002296
Train Epoch: 20 [800/2399 (33%)] Loss: 0.000026
Train Epoch: 20 [1200/2399 (50%)] Loss: 0.000077
Train Epoch: 20 [1600/2399 (67%)] Loss: 0.000241
Train Epoch: 20 [2000/2399 (83%)] Loss: 0.001908
Train Epoch: 20 [1800/2399 (100%)] Loss: 0.001944
Test set: Average loss: 0.1191, Accuracy: 535/597 (90%)
```

학습 결과를 비교해보면 20 epochs만 학습했는데도 정확도가 훨씬 올라간 것을 볼 수 있습니다. 전이 학습을 시킨 모델도 test dataset 으로 평가를 해보겠습니다. 

<img src="/assets/12_DL image classification/inference-TL.png" alt="inference-TL">


고양이 품종을 잘 분류하고 있네요! 이 정도면 학습이 잘 된 것 같습니다~ 
정확도를 더 올리기 위한 여러 가지 다른 방법들이 있지만, 이번 포스트에서는 여기서 마무리 하도록 하겠습니다.

<br>

### 4. Endpoint 삭제하기
추론을 끝냈다면, endpoint를 삭제하여 연관된 인스턴스를 해제합니다.

```python
estimator.delete_endpoint()
```
<br>
<br>

## 끝내며...
지금까지 Amazon Sagemaker에서 PyTorch 프레임워크를 활용하여 간단한 딥러닝 이미지 분류 모델을 만들어 보았습니다. 다음 기회에는 이렇게 학습에서 배포까지 한번에 처리하여 생성된 Endpoint와 Django Rest Framework(DRF)를 사용하여 Severless RESTful API를 개발하는 방법에 대해 알아보도록 하겠습니다.


