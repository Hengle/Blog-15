# Games 202

## Introduction and Overview

### Real-Time High Quality Rendering

- Real-Time：speed Interactivity
- High Quality: Realism, Dependability

### Topics

- Shadow and Environment Mapping
- Global illumination
- Precomputed Radiance Transfer
- Real -Time Ray Tracing
- Participating Media Rendering, Image Space Effects
- Non-Photorealistic Rendering
- Antialiasing and supersampling

## Recap of CG Basics

1. Graphics Pipline
2. OpenGL
3. Shader Languages
4. The Rendering Equation
5. Environment Lighting

## Real-Time Shadow

### shadow Mapping

#### algorithm

1. Render from Light
2. Render form Eye
3. Project to light from shadows

#### Issues in Shadow Mapping

1. Self occlusion ：shadowmap 分辨率造成自遮挡条纹，shadow map 上一个像素表示的表面距离只是实际表示平面的中心点距离
   1. Bias 解决自遮挡问题，会有漏光问题
   2. Second-depth shadow mapping，实际上没人用
2. Aliasing：shadowmap 分辨率造成锯齿，
   1. 使用级联阴影解决，cascade shadowmap

### Inequalities in Calculus (WIT)

### Soft Shadow

#### Percentage Close Filter (PCF)

使用卷积核，对 shadow map 比较后的结果进行平均

1. compare its depth with all pixels in the box 
2. get the compared results
3. take avg to get visibility atten

#### Percentage closer soft shadows (PCSS)

离遮挡物越远使用 PCF 的卷积核就越大

1. Blocker search
2. Penumbra estimation
3. Percentage closer filtering

Slow steps

1. 像素点 Blocker 太大导致采样多速度慢
2. 通过稀疏采样提速，可能出现 Flicker，每帧的噪声不同导致画面抖动

####  Variance soft shadow mapping (VSM)

使用正态分布近似 Blocker 中的阴影分布

Quickly compute the mean and variance of depths in an area

- Mean : Map pre compute
  - Hardware  MIPMAPing
  - Summed Area Tables （SAT）

- Variance
  - Var(X) = E(X^2) - E(X)^2
  - generate a square-depth map along with the shadow map

##### Function

- PDF : 概率分布函数，Gaussian 是一种 PDF
- CDF : 概率积分函数，误差函数

对于 Shadow map 上的每个点，都可以构建一个 PCF，对应的 CDF 为物体处在阴影中的概率。

根据这个概率来决定光线的衰减值，PCF 是通过 Blocker Search 的方式来通过像素点测试和权重累加来确定概率。

##### 切比雪夫不等式 （WIP）

快速得到当前距离大于 ShadowMap 距离的概率，非常的近似。

容易出现 Light Leaking，当遮挡物分布与高斯分布不符合

#### Moment Shadow Mapping

使用多项式来近似 PCF

#### Distance field soft shadow

速度快，效果好，但是存储困难

##### Distance Functions

定义空间中的一个点，到任意物体表面的总的最小距离

##### Ray Matching

光线步进，向摄像机往像素点射出的方向，让光线前进一段距离，并对当前位置进行采样。

##### Usage

生成 3D 纹理存储场景 SDF，光线步进 SDF 采样出的距离，如果到目标光栅化物体深度前，进入其他物体内部，说明处在阴影中。

受光强度可以通过最小 SDF 距离得到。

- Pros
  - Fast
  - High quality
- Cons
  - Precomputation
  - heavy stoage
  - Artifact

## Real-Time Environment Mapping

### Environment Lighting

#### Image base lighting (IBL)

基于图的环境光，可以为整个场景原点烘培一个 CubeMap，表示 Environment map，也可以为环境中的一个点烘培 EnvMap

EnvMap 可以是天空盒，也可以是实际用摄像机渲染出的 6 面的结果，缺点是不知道光源距离

#### The Classic Approxiamtion

对物体表面的渲染要考虑到整个环境的光照对其的贡献，蒙特卡洛积分需求对 EnvMap 进行多次采样，所以提前对 EnvMap 生成 MipMap，提前对其模糊。

同时可以预计算出 BRDF 对 Roughness 和 Cosθ 的 LUT

### Shadow from Environment Lighting

多光源的可见性不好表达，需要大量 ShadowMap，当前是使用一个直接光的级联阴影作为主光源阴影，另外用一张大的合并的 shadow map 做点光源和聚光源阴影。

### Fourier Transform 

高频信号能用多个低频信号累加来表达

时域上的卷积为频域上的乘积

频域上的相乘具有滤波意义，只留下低频或高频信号

### Shperical Harmonics 球面斜坡函数球谐函数

SH 是定义在球面上的一组正交基函数 f(θ, φ)，即球面上的低频信号。

三阶 SH 可以还原成模糊的 EnvMap 中的一个通道，

三阶 SH 9 个系数，还原成 3 通道 EnvMap 需要 27 个系数，即 7 个 float4

每个 Reflection Probe 都会使用 7 个 float4 拟合烘培好的 EnvMap 