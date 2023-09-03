## Illumination

### Basement

#### 立体角

理解球面坐标$(r,\theta,\varphi)$几个微分

- 径向微分$\mathrm{d}r$
- 切向微分$r\mathrm{d}\theta$
- 垂直径向和切向所在平面的微分$r\sin\theta\mathrm{d}\varphi$

因此，径向面积(面元)微分
$$
\mathrm{d}A=r\mathrm{d}\theta\cdot r\sin\theta\mathrm{d}\varphi=r^2\sin\theta
\mathrm{d}\theta\mathrm{d}\varphi
$$
注意到这里和$r$有关，消去$r^2$可以得到立体角的定义
$$
\mathrm{d}\omega=\frac{\mathrm{d}A}{r^2}=
\sin\theta\mathrm{d}\theta\mathrm{d}\varphi
$$
立体角$\mathrm{d}\omega$本质上是单位球面的面元的关于$(\theta,\varphi)$的微分，因为$r=1$已经确定，少了一个自由度

#### 投影面积

对于面元$\mathrm{d}A$在于面法线方向成$\theta$角的方向上投影面积（即从该方向观察面$A$的实际面积微元）

为啥是$\cos\theta$，简单理解就是垂直时能看到全部，平行时完全看不到
$$
\mathrm{d}A_{proj}=\cos\theta\mathrm{d}A
$$

#### 能量

辐射通量Radiant Flux
$$
\Phi=\frac{\mathrm{d}Q}{\mathrm{d}t}
$$
辐射强度Radiant Intensity
$$
I=\frac{\mathrm{d}\Phi}{\mathrm{d}\omega}
$$
辐射率Radiance，概念比较抽象，简单说就是辐射通量$\phi$在投影面积$\mathrm{d}A_{proj}$和立体角$\mathrm{d}{\omega}$两个方向的变化率
$$
L=\frac{\mathrm{d}I}{\mathrm{d}A_{proj}}=\frac{\mathrm{d}^2\Phi}{\cos\theta\mathrm{d}A\mathrm{d}\omega}
$$
辐照度Irradiance，即单位面积的射入表面的辐射通量
$$
E=\frac{\mathrm{d}\Phi}{\mathrm{d}A}
$$
等价于入射光Radiance在半球面对$\omega$的积分

因为Radiance和Irradiance定义的一个是投影面积，一个是实际面积，用$\cos\theta$是修正下
$$
E=\frac{\mathrm{d}\Phi}{\mathrm{d}A}=\int_{\Omega}L(\omega)\cos\theta\mathrm{d}\omega
$$

### BRDF

#### 定义

BRDF(Bidirectional Reflectance Distribution Function)定义是出射辐射率Radiance微分和入射辐照度Irradiance微分之比
$$
f(\omega_i,\omega_o)=\frac{\mathrm{d}L_o(\omega_o)}{\mathrm{d}E_i(\omega_i)}
$$
简单变换得到渲染方程
$$
\mathrm{d}L_o(\omega_o)=f(\omega_i,\omega_o)\mathrm{d}E_i(\omega_i)
$$
即
$$
L_o(\omega_o)=
\int_{\Omega}f(\omega_i,\omega_o)\cdot L_i(\omega_i)\cos\theta_i\mathrm{d}\omega_i
$$

#### 漫反射

简单的漫反射模型认为BRDF和方向无关，Radiance也和方向无关，都是常数，且$L_i=L_o$
$$
L_o(\omega_o)=f_{d}L_i\int_{\Omega}\cos\theta_i\mathrm{d}\omega_i
$$
简单计算下
$$
L_o(\omega_o)=f_{d}L_i\int_{\varphi_i=0}^{\varphi_i=2\pi}
\int_{\theta_i=0}^{\theta_i=\frac{\pi}{2}}
sin\theta_i\cos\theta_i\mathrm{d}\theta_i\mathrm{d}\varphi_i
$$

$$
L_o(\omega_o)=f_{d}L_i\int_{\varphi_i=0}^{\varphi_i=2\pi}
\left(-\frac{1}{4}\cos2\theta_i\right)_0^{\frac{\pi}{2}}\mathrm{d}\varphi_i
=\pi f_{d}L_i
$$

即
$$
f_{d}=\frac{Lo}{\pi L_i}=\frac{1}{\pi}
$$
通常使用albedo作为漫反射材质反射的颜色，即
$$
f_d=\frac{\mathrm{albedo}}{\pi}
$$

#### 微平面高光

记
$$
\textbf{h}=\frac{\textbf{l}+\textbf{v}}{2}
$$
根据Cook-Torrance模型
$$
f_{s}(\textbf{l},\textbf{v})=\frac
{F(\textbf{h},\textbf{l})\cdot 
G(\textbf{l},\textbf{v},\textbf{n})\cdot D(\textbf{n},\textbf{h})}
{4\left|\textbf{n}\cdot \textbf{l}\right|\left|\textbf{n}\cdot \textbf{v}\right|}
$$
$F(\textbf{h},\textbf{l})$是反射和入射的Irradiance之比，理论公式比较复杂，通常使用Schlick近似，这里$F_0$是基础反射率
$$
F(\textbf{h},\textbf{l})\approx F_0+(1-F_0)(1-\textbf{h}\cdot\textbf{l})^5
$$
$G(\textbf{l},\textbf{v},\textbf{n})$从统计学上近似的求得了由于粗糙度导致微平面间相互遮蔽导致的能量损失，对于一个输入的粗糙度$\alpha$，需要从映射到$k$
$$
\left\{\begin{matrix}
k_{Direct}=\frac{(\alpha+1)^2}{8}
\\
k_{IBL}=\frac{\alpha^2}{2}
\end{matrix}\right.
$$
通常使用Schlick GGX作为估计
$$
G_{S}(\textbf{n},\textbf{v})=\frac{\textbf{n}\cdot \textbf{v}}{\textbf{n}\cdot \textbf{v}(1-k)+k}
$$
需要估计观察方向$\textbf{v}$和入射方向$\textbf{l}$的能量损失总和
$$
G(\textbf{l},\textbf{v},\textbf{n})=G_{S}(\textbf{n},\textbf{v})\cdot 
G_{S}(\textbf{n},\textbf{l})
$$
$D(\textbf{n},\textbf{h})$近似表示了微平面法线与$\textbf{h}$取向一致的概率，对于一个输入的粗糙度$\alpha$，如果使用Trowbridge-Reitz GGX
$$
D(\textbf{h})=\frac{\alpha^2}{\pi((\textbf{n}\cdot\textbf{h})^2(\alpha^2-1)+1)^2}
$$

### IBL

$$
L_o(\omega_o)=
\int_{\Omega}f(\omega_i,\omega_o)\cdot L_i(\omega_i)\cos\theta_i\mathrm{d}\omega_i
$$

$$
L_o(\omega_o)\approx
\frac{\int_{\Omega}L_i(\omega_i)\mathrm{d}\omega_i}{\int_{\Omega}\mathrm{d}\omega_i}
\int_{\Omega}f(\omega_i,\omega_o)\cdot \cos\theta_i\mathrm{d}\omega_i
$$

使用Cook-Torrance模型，把基础反射率$F_0$拆出来
$$
\int_{\Omega}f(\omega_i,\omega_o)\cdot \cos\theta_i\mathrm{d}\omega_i
\approx
\int_{\Omega}\frac{f}{F}
\left(F_0+(1-F_0)(1-\textbf{h}\cdot\textbf{l})^5\right)\cos\theta_i\mathrm{d}\omega_i
$$

$$
\int_{\Omega}f(\omega_i,\omega_o)\cdot \cos\theta_i\mathrm{d}\omega_i
\approx
\int_{\Omega}\frac{f}{F}
\left(F_0(1-(1-\textbf{h}\cdot\textbf{l})^5+(1-\textbf{h}\cdot\textbf{l})^5\right)
\cos\theta_i\mathrm{d}\omega_i
$$

$$
\int_{\Omega}f(\omega_i,\omega_o)\cdot \cos\theta_i\mathrm{d}\omega_i
\approx
F_0\int_{\Omega}\frac{f}{F}
\left(1-(1-\textbf{h}\cdot\textbf{l})^5\right)\cos\theta_i\mathrm{d}\omega_i
+
\int_{\Omega}\frac{f}{F}(1-\textbf{h}\cdot\textbf{l})^5
\cos\theta_i\mathrm{d}\omega_i
$$

化简后BRDF积分预计算只和$(\alpha,\cos\theta)$有关

### Precomputed Radiance Transfer

#### Introduction

由于渲染方程直接预计算需要存储大量数据（添加Visibility项$V(\omega_i)$表示Shadow）
$$
L_o(\omega_o)=
\int_{\Omega}f(\omega_i,\omega_o)\cdot L_i(\omega_i)V(\omega_i)\cos\theta_i\mathrm{d}\omega_i
$$
拆分为Lighting项$L_i(\omega_i)$和Light transport项$f(\omega_i,\omega_o)V(\omega_i)\cos\theta_i$

使用Sphere Harmonics作为基函数$B_i(\omega)$
$$
L_i(\omega_i)\approx \sum_{m=0}^{M}p_mB_m(\omega_i)
$$

$$
f(\omega_i,\omega_o)V(\omega_i)\cos\theta_i\approx \sum_{n=0}^{N}q_nB_n(\omega_i)
$$

其中基函数系数计算方式如下
$$
p_m=\int_\Omega f(\omega)B_m(\omega)\mathrm{d}\omega
$$
带入有
$$
L_o(\omega_o)\approx \int_{\Omega}\sum_{m=0}^{M}p_mB_m(\omega_i)\cdot \sum_{n=0}^{N}q_nB_n(\omega_i)\mathrm{d}\omega_i
$$
由于$p_m,q_n$为常系数，把$\sum$提到外面
$$
L_o(\omega_o)\approx
\sum_{m=0}^{M}\sum_{n=0}^{N}p_m q_n\int_{\Omega}B_m(\omega_i)\cdot
B_n(\omega_i)\mathrm{d}\omega_i
$$
由于Sphere Harmonics相互正交
$$
\int_{\Omega}B_m(\omega_i)\cdot B_n(\omega_i)\mathrm{d}\omega_i=
\left\{\begin{matrix}
1&m=n \\
0&m\neq n 
\end{matrix}\right.
$$
即
$$
L_o(\omega_o)\approx\sum_{m=0}^{\min(M,N)}p_mq_n
$$

#### Implement

采样CubeMap的时候，使用的方向是归一化的向量
$$
\frac{(x,y,1)}{(x^2+y^2+1)^{\frac{1}{2}}}
$$
令
$$
\left\{\begin{matrix}
F_x=x(x^2+y^2+1)^{-\frac{1}{2}}\\
F_y=y(x^2+y^2+1)^{-\frac{1}{2}}\\
F_z=(x^2+y^2+1)^{-\frac{1}{2}}
\end{matrix}\right.
$$

$$
\frac{\partial F_x}{\partial x}=(x^2+y^2+1)^{-\frac{1}{2}}
+x\left[-\frac{1}{2}(x^2+y^2+1)^{-\frac{3}{2}}\cdot(2x)\right]
=\frac{y^2+1}{(x^2+y^2+1)^{\frac{3}{2}}}
$$

$$
\frac{\partial F_y}{\partial x}=-\frac{xy}{(x^2+y^2+1)^{\frac{3}{2}}}
$$

$$
\frac{\partial F_z}{\partial x}=-\frac{x}{(x^2+y^2+1)^{\frac{3}{2}}}
$$

即
$$
\frac{\partial F}{\partial x}=\frac{\partial F_x}{\partial x}\textbf{e}_x
+\frac{\partial F_y}{\partial x}\textbf{e}_y
+\frac{\partial F_z}{\partial x}\textbf{e}_z
=\frac{(y^2+1,-xy,-x)}{(x^2+y^2+1)^{\frac{3}{2}}}
$$
同理
$$
\frac{\partial F}{\partial y}=\frac{\partial F_x}{\partial y}\textbf{e}_x
+\frac{\partial F_y}{\partial y}\textbf{e}_y
+\frac{\partial F_z}{\partial y}\textbf{e}_z
=\frac{(-xy,x^2+1,-y)}{(x^2+y^2+1)^{\frac{3}{2}}}
$$
即
$$
\mathrm{d}A=\left|\frac{\partial F}{\partial x}\times\frac{\partial F}{\partial y}\right|
\mathrm{d}x\mathrm{d}y
=\frac{1}{(x^2+y^2+1)^{\frac{3}{2}}}\mathrm{d}x\mathrm{d}y
$$
计算$x\in(0,m),y\in(0,n)$的面积$A(m,n)$
$$
\int_{y=0}^{n}\int_{x=0}^{m}\frac{1}{(x^2+y^2+1)^{\frac{3}{2}}}\mathrm{d}x\mathrm{d}y
$$
先计算关于$x$的不定积分$F_x(x)$
$$
F_x(x)=\int\frac{1}{(x^2+y^2+1)^{\frac{3}{2}}}\mathrm{d}x
$$
令$x=\tan(t)\sqrt{y^2+1}$，带入有
$$
\mathrm{d}\tan(t)=\frac{1}{\cos^2(t)}=\sec^2(t)
$$

$$
\mathrm{d}x=\sec^2(t)\sqrt{y^2+1}\mathrm{d}t
$$

等价计算关于$t$的不定积分$F_t(t)$
$$
F_t(t)=F_x(x)=\int\frac{1}{(x^2+y^2+1)^{\frac{3}{2}}}\mathrm{d}x=
\int\frac{\sec^2(t)\sqrt{y^2+1}}
{\left((\tan^2(t)(y^2+1)+y^2+1\right)^{\frac{3}{2}}}\mathrm{d}t
$$

$$
F_t(t)=\int\frac{\sec^2(t)\sqrt{y^2+1}}
{\left((\tan^2(t)+1)(y^2+1)\right)^{\frac{3}{2}}}\mathrm{d}t
=\int\frac{\sec^2(t)\sqrt{y^2+1}}
{\left(\sec^2(t)(y^2+1)\right)^{\frac{3}{2}}}\mathrm{d}t
$$

$$
F_t(t)=\int\frac{\sec^2(t)\sqrt{y^2+1}}
{\sec^3(t)(y^2+1)^{\frac{3}{2}}}\mathrm{d}t
=\int\frac{1}{\sec(t)(y^2+1)}\mathrm{d}t
=\frac{1}{y^2+1}\int\cos(t)\mathrm{d}t
$$

化简得
$$
F_t(t)=\frac{\sin(t)}{y^2+1}+C
$$
分析上下限，明显
$$
t_{min}=0\\
$$

$$
t_{max}=\arctan(\frac{m}{\sqrt{y^2+1}})
$$

即
$$
F_t(t_{max})-F_t(t_{min})
=\frac{\sin\left(\arctan(\frac{m}{\sqrt{1+y^2}})\right)}{y^2+1}-0
$$
令$u=\arctan(\frac{m}{\sqrt{y^2+1}})$，即$\tan(u)=\frac{m}{\sqrt{y^2+1}}$
$$
\cos^2(u)=\frac{1}{1+\tan^2(u)}=\frac{y^2+1}{y^2+m^2+1}
$$

$$
\sin(u)=\sqrt{1-\cos^2(u)}=\frac{m}{\sqrt{y^2+m^2+1}}
$$

带入有
$$
\int_{0}^{m}\frac{1}{(x^2+y^2+1)^{\frac{3}{2}}}\mathrm{d}x=F_t(t_{max})-F_t(t_{min})=
\frac{m}{(y^2+1)\sqrt{y^2+m^2+1}}
$$
因此
$$
\int_{y=0}^{n}\int_{x=0}^{m}\frac{1}{(x^2+y^2+1)^{\frac{3}{2}}}\mathrm{d}x\mathrm{d}y
=
\int_{y=0}^{n}\frac{m}{(y^2+1)\sqrt{y^2+m^2+1}}\mathrm{d}y
$$
令$y=\tan(v)\sqrt{m^2+1}$，带入有
$$
\mathrm{d}y=\sec^2(v)\sqrt{m^2+1}\mathrm{d}v
$$
把积分式分子的$m$提到外面，等价计算不定积分$F_v(v)$
$$
F_v(v)=F_y(y)=m\int\frac{1}{(y^2+1)\sqrt{y^2+m^2+1}}\mathrm{d}y
$$

$$
F_v(v)=
m\int\frac{\sec^2(v)\sqrt{m^2+1}}
{\left(\tan^2(v)(m^2+1)+1\right)\sqrt{\tan^2(v)(m^2+1)+m^2+1}}\mathrm{d}v
$$

$$
F_v(v)=
m\int\frac{\sec^2(v)\sqrt{m^2+1}}
{\left(\tan^2(v)(m^2+1)+1\right)\sec(v)\sqrt{m^2+1}}\mathrm{d}v
$$

$$
F_v(v)=m\int\frac{\sec(v)}{\tan^2(v)(m^2+1)+1}\mathrm{d}v
$$

$$
F_v(v)=m\int\frac{\sec(v)\cot^2(v)}
{\left(\tan^2(v)(m^2+1)+1\right)\cot^2(v)}\mathrm{d}v
$$

$$
F_v(v)=m\int\frac{\cot(v)\csc(v)}{m^2+1+\cot^2(v)}\mathrm{d}v=
m\int\frac{\cot(v)\csc(v)}{m^2+\csc^2(v)}\mathrm{d}v
$$

令$w=\csc(v)$
$$
\mathrm{d}w=d\csc(v)=\left(\frac{1}{\sin(v)}\right)'\mathrm{d}v
=-\cot(v)\csc(v)\mathrm{d}v
$$

$$
\mathrm{d}v=-\frac{\mathrm{d}w}{\cot(v)\csc(v)}
$$


$$
F_w(w)=m\int\frac{\cot(v)\csc(v)}{m^2+w^2}
\cdot\left(-\frac{1}{\cot(v)\csc(v)}\right)\mathrm{d}w
$$

$$
F_w(w)=-m\int\frac{1}{m^2+w^2}\mathrm{d}w=
-\int\frac{1}{\left(\frac{w}{m}\right)^2+1}\mathrm{d}(\frac{w}{m})
$$

即
$$
F_w(w)=-\arctan(\frac{w}{m})+C
$$

$$
F_v(v)=-\arctan(\frac{\csc(v)}{m})+C=-\arctan(\frac{1}{m\sin(v)})+C
$$

由于$y=\tan(v)\sqrt{m^2+1}$

即
$$
\sin(v)=\sqrt{1-\cos^2(v)}=\sqrt{1-\frac{1}{1+\tan^2(v)}}=\frac{y}{\sqrt{y^2+m^2+1}}
$$
带入有
$$
F_y(y)=-\arctan\left(\frac{1}{m\frac{y}{\sqrt{y^2+m^2+1}}}\right)+C
=-\arctan\left(\frac{\sqrt{y^2+m^2+1}}{my}\right)+C
$$
由于
$$
\arctan(x)+\arctan(\frac{1}{x})=\frac{\pi}{2}
$$
即
$$
F_y(y)=\arctan\left(\frac{\sqrt{y^2+m^2+1}}{my}\right)^{-1}-\frac{\pi}{2}+C
$$

$$
F_y(y)=\arctan\left(\frac{my}{\sqrt{y^2+m^2+1}}\right)+C
$$

即
$$
\int_{y=0}^{n}\int_{x=0}^{m}\frac{1}{(x^2+y^2+1)^{\frac{3}{2}}}\mathrm{d}x\mathrm{d}y
=F_y(n)-F_y(0)=\arctan\left(\frac{mn}{\sqrt{m^2+n^2+1}}\right)
$$
