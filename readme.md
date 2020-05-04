
## 밑에 변수및 함수들 설명

<p>real : opt.input_dir 과 opt.input_name을 이용하여 image를 array형식으로 받아옴 (크기 : 1*3*186*248  186*248은 사진 크기)</p>
<p>functions.adjust_scale2Image(real, opt) : opt를 설정해줌</p>
<p>function.load_trained_pyramid : 총 4개의 모델들을 불러옴</p>
    
- Gs : generator 모델
- Zs : noise, 왠지는 모르겠지만 같은 scale일 경우 noise의 크기가 더큼
- reals : 각 스케일별로 그에 맞는 스케일의 이미지들,  값의 크기는 원본 이미지이 scale 된 size와 같음 
- NoiseAmp : noise의 진폭
- in_s : reals의 가장 낮은 해상도, 즉 가장 적은 픽셀수를 가진 reals[0]에 각각 scale_v배 ale_h배 한것, 일단 0으로 초기화 되어 있음



### 각각을 출력해보았을 경우
* real.shape

        torch.Size([1, 3, 186, 248])

* reals[0].shape, reals[1].shape, reals[8].shape

        torch.Size([1, 3, 25, 34]) torch.Size([1, 3, 33, 43]) torch.Size([1, 3, 186, 248])

* Zs[0].shape, Zs[1].shape, Zs[8].shape

        torch.Size([1, 3, 35, 44]) torch.Size([1, 3, 43, 53]) torch.Size([1, 3, 196, 258])
        

* NoiseAmp

        [1, tensor(0.0240, device='cuda:0'), tensor(0.0106, device='cuda:0'), tensor(0.0082, device='cuda:0'), tensor(0.0080, device='cuda:0'), tensor(0.0076, device='cuda:0'), tensor(0.0071, device='cuda:0'), tensor(0.0071, device='cuda:0'), tensor(0.0093, device='cuda:0')]'

* ins

        in_s :  tensor([[[[0., 0., 0.,  ..., 0., 0., 0.],
                [0., 0., 0.,  ..., 0., 0., 0.],
                [0., 0., 0.,  ..., 0., 0., 0.],
                ...,
                [0., 0., 0.,  ..., 0., 0., 0.],
                [0., 0., 0.,  ..., 0., 0., 0.],
                [0., 0., 0.,  ..., 0., 0., 0.]],

                [[0., 0., 0.,  ..., 0., 0., 0.],
                [0., 0., 0.,  ..., 0., 0., 0.],
                [0., 0., 0.,  ..., 0., 0., 0.],
                ...,
                [0., 0., 0.,  ..., 0., 0., 0.],
                [0., 0., 0.,  ..., 0., 0., 0.],
                [0., 0., 0.,  ..., 0., 0., 0.]],

                [[0., 0., 0.,  ..., 0., 0., 0.],
                [0., 0., 0.,  ..., 0., 0., 0.],
                [0., 0., 0.,  ..., 0., 0., 0.],
                ...,
                [0., 0., 0.,  ..., 0., 0., 0.],
                [0., 0., 0.,  ..., 0., 0., 0.],
                [0., 0., 0.,  ..., 0., 0., 0.]]]], device='cuda:0')

* in_s.sum()

        tensor(0., device='cuda:0')


 ## reals와 Zs를 시각화 할 경우
 

 * 원본

    ![balloons (1)](https://user-images.githubusercontent.com/12128784/80305347-2e333080-87f7-11ea-8863-3aedec02137e.png)

* reals[0,1,2,3,4,8] zs[0,1,2,3,4,8]

    ![image](https://user-images.githubusercontent.com/12128784/80310453-d48e2e80-8815-11ea-9962-4d8c74946619.png)




   
       
         






































# adjust_scales2image(real_, opt)

```
이 함수 안에서는 real값을 가지고 opt에 관한 설정을 해준다.

이 함수는 가장 큰 스케일의 이미지 크기가 --min_size이고 가장 작은 크기인 --max_size가 되도록 --scale_factor에 가능한 가장 가까운 scale_factor를 계산한다.
```

## 변수 설명
        아래에서 설명의 편의를 위하여 1000*500 size의 image를 input으로, 
        0.75를 scale_factor_init로 놓고 예제를 구현하겠다.
        opt.min_size : 25
        opt.max_size : 200

## *real_*    

        원본 이미지, real_.shape[2] : 세로의 길이, real_.shape[3] : 가로의 길이

## *opt.scale_factor*    

        pyramid scale factor , default = 0.75

## *opt.min_size*

        이미지의 가장 작은 스케일의 크기

## *opt.num_scales* (W : 원본 이미지의 가로, H : 원본 이미지의 세로)    
![image](https://user-images.githubusercontent.com/12128784/80965375-5f41df80-8e4d-11ea-8b8c-8af63333212f.png)
![image](https://user-images.githubusercontent.com/12128784/80963925-ec376980-8e4a-11ea-91b4-d77f079ff869.png)


## *scale2stop*    
        
        scale2stop은 --max_size에 도달하는 데 필요한 피라미드 레벨의 수이다.
    
![image](https://user-images.githubusercontent.com/12128784/80958489-16375e80-8e40-11ea-84b5-26639c68efb9.png)    
![image](https://user-images.githubusercontent.com/12128784/80966991-9796ed00-8e50-11ea-83ef-b2e23b8ce34c.png)


## *opt.stop_scale*

![image](https://user-images.githubusercontent.com/12128784/80937871-c3df4900-8e11-11ea-8358-a8fd72351e90.png)    
![image](https://user-images.githubusercontent.com/12128784/80967090-c0b77d80-8e50-11ea-9cf6-2ec560dd518c.png)




## *opt.scale1*

![image](https://user-images.githubusercontent.com/12128784/80958951-cefd9d80-8e40-11ea-8daa-aadca36cbf4b.png)    
![image](https://user-images.githubusercontent.com/12128784/80967206-ea70a480-8e50-11ea-8743-fc2f43f59877.png)



## *real*
    
        real은 real_에 opt.scale1배한 만큼 증가한다.    
![image](https://user-images.githubusercontent.com/12128784/80964720-556bac80-8e4c-11ea-8bfa-404c01529fb1.png)
![image](https://user-images.githubusercontent.com/12128784/80967381-41767980-8e51-11ea-90be-c4cdf036d862.png)

## *opt.scale_factor* (H' : real의 세로, W' : real의 가로)

![image](https://user-images.githubusercontent.com/12128784/80959211-4a5f4f00-8e41-11ea-92e7-d31001266a5c.png)    
![image](https://user-images.githubusercontent.com/12128784/80967572-8dc1b980-8e51-11ea-9eee-99cf116e55ff.png)


## *scale2stop*

![image](https://user-images.githubusercontent.com/12128784/80958489-16375e80-8e40-11ea-84b5-26639c68efb9.png)    
![image](https://user-images.githubusercontent.com/12128784/80966991-9796ed00-8e50-11ea-83ef-b2e23b8ce34c.png)

## *opt.stop_scale*    

![image](https://user-images.githubusercontent.com/12128784/80937871-c3df4900-8e11-11ea-8358-a8fd72351e90.png)    
![image](https://user-images.githubusercontent.com/12128784/80967090-c0b77d80-8e50-11ea-9cf6-2ec560dd518c.png)
