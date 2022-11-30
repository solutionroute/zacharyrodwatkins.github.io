---
title: "Robotic Zipping Air Hockey (RZA)"
excerpt: "RZA is a fully autonomous Air Hockey Opponent <br/><img src='/images/michelle_ai.png'>"
collection: portfolio
---

<br/><img src='/images/michelle_ai.png'>
 

We were tasked by the UBC Engineering Physics Project Lab to develop a platform for a next generation AI and Automation course. Over the last two years we have designed and built an automated air hockey table for which students are able to implement their own agents to compete against human opponents.

Robotic zippin’ air hockey - RZA - is an H-Bot gantry system capable of playing air hockey with human-like performance. We have designed a plug-and-play interface which separates the control into two distinct sections: high level control, and low level control. The high level control is responsible for choosing paths for the mallet to follow based on the current state of the game. The low level control is responsible for executing the chosen paths to high accuracy. This interface allows AI development students to not worry about the details of moving the gantry while developing their high level decision making systems.

The RZA allows students test and compare their respective agents on a real physical system. This will lead to an exciting final evaluation for students in this AI course as the performance of their decision making system is judged in a 1v1 competition format.\(.*\.jpg

With a rudimentary high level decision making system, the RZA can play a human in air hockey. It can comfortably defend its net, and can score the occasional goal. 


You can download a detailed account of this project [here](https://zacharyrodwatkins.github.io/files/RZA.pdf)
Relavent source-code is available [here](https://github.com/zacharyrodwatkins/PucksInDeep)


Just to highlight one of my personal favourite parts of this project, here's an excerpt from the above paper regarding the work I did to model the physics of out robot



## Modeling the Electrodynamics of the Gantry


This document describes the modeling we undertook of the electrodynamics of our gantry. It was intended for use amongst our team and the terminology reflects that. 

### {Modeling the Mechanics}

We did this by writing down the Lagrangian of the system in terms of generalized coordinates $\theta = (\theta_1,\theta_2)^T$. One can easily transform from $X=(x,y)^T$ by using:

$$
 \begin{bmatrix} x \\ y \end{bmatrix}
 =
 \frac{R_p}{2}
  \begin{bmatrix}
   1 & 1 \\ 1 & -1
   \end{bmatrix}
    \begin{bmatrix} \theta_1 \\ \theta_2 \end{bmatrix} \label{basis}
    :=E\theta
$$


We then want to find the Lagrangian $L$. This is gross if you actually do it in terms of the masses and inertia of the components. We've done it, its somewhere, and if you really want it I'll dig it up. But I don't want to spend all of my twenties on latex, so let's just skip that. The important bit is that we are able to spot a symmetry in our system. Since nothing depends on position, and the system is symmetric w.r.t. $\theta 1$ and $\theta 2$, we can skip all that and just write 

$$
 L
 =
 \frac{1}{2}
  \begin{bmatrix}
   J_l & J_s \\ J_s & J_l
   \end{bmatrix}
    \dot{\theta}^2
$$

Which give that the torque $\tau$ we need is found by finding E.L. equations. This gives:

$$
    \tau=  \begin{bmatrix}
   J_l & J_s \\ J_s & J_l
   \end{bmatrix}\ddot\theta
$$

This equation neglects friction, because our Lagrangian derivation neglected it, because Lagrangian mechanics is stupid that way. But since we aim to be deriving all the inertia empirically anyways, this isn't a big deal. Well just tack on a friction term, and the argument from symmetry still applies, so the equations for dynamics should hold. We will have friction terms proportional to $\theta$ and $X$. We consider the frictions in our motors to be identical. This is still a pretty big simplification, since there will be a friction term proportional to the speed (i.e. $\sqrt{\dot{x}^2 + \dot{y}^2}$) resulting from the mallet on the play surface. Being air-hockey, this surface will be slippery, so hopefully this is negligible.

$$\tau_f =  
\begin{bmatrix}
   b & 0 \\ 0 & b
\end{bmatrix}\dot\theta
+
\begin{bmatrix}
   f_x & 0 \\ 0 & f_y
\end{bmatrix}_{x,y}\dot{X}
$$

We need to change the basis of the second matrix, as well as convert $X$ to $\theta$. Using the change of basis matrix E \eqref{basis} we have

$$\tau_f =  
\begin{bmatrix}
   b & 0 \\ 0 & b
\end{bmatrix}\dot\theta
+
E^{-1}\begin{bmatrix}
   f_x & 0 \\ 0 & f_y
\end{bmatrix}_{x,y}E\theta
$$
This gives a symmetric matrix like the one for inertia. Renaming our friction coefficients to match l,s convention, and putting this together we find an O.D.E relating the driving torque to our angle:

\begin{gather}
    \tau=  \begin{bmatrix}
   J_l & J_s \\ J_s & J_l
   \end{bmatrix}\ddot\theta
    +\begin{bmatrix}
       b_l & b_s \\ b_s & b_l
    \end{bmatrix}\dot\theta\label{torque}
\end{gather}

\section{Modeling the Motors}

To be able to do anything useful with this model we have to figure out how much torque we are able to produce. Typically, the torque a motor provide is proportional to the current flowing through it: $\tau = kI$. Now, if we were able to control the current directly that'd be problem solved. But we can't; we can only control the voltage, and let the current be what it may. Thus, we need to model the relationship between current and voltage. Modeling the motor as an LR circuit, and taking into account the back EMF, we have

\begin{gather}
   L\dot{I} + RI = V - K\dot\theta \label{I}
\end{gather}


This constitutes our model for the model. Since we are treating the motors as being identical, L and R are both scalars, while V,I and $\theta$ are vectors. We are left with a system of two (matrix) O.D.Es to solve. This is done by moving to the Laplace domain. 


\section{Laplace domain}
    We wish to convert \eqref{torque}, \eqref{I} to the Laplace domain, so that we can deal with them algebraically. For now, we will assume 0 initial conditions - so long as this is reflected in the physical test we do, we will be able to extract the quantities we need. We can add the initial conditions terms in later for cases where they do not vanish.
    
    In the Laplace domain, with vanishing I.C.s, \eqref{torque} becomes:
    
    $$
        \tau = (Js^2+Bs)\theta
    $$
    
    Here, J and b are the matrices from \eqref{torque}. Likewise \eqref{I} becomes:
    $$
        (Ls+R)I=V-Ks\theta
    $$
    
    Rearranging gives:
    $$
    I = \dfrac{V-Ks\theta}{Ls+R}
    $$
    
    Using $\tau = KI$ we can then write:
    
    $$
    K\dfrac{V-Ks\theta}{Ls+R} = (Js^2+bs)\theta
    $$
    
    And so:
    
    $$
        V = \left[\dfrac{Ls+R}{K}(Js^2+bs)+Ks\right]\theta
    $$
    
    After expanding we see and grouping like terms we see:
    
    \begin{gather}
        V = \left[\dfrac{LJ}{K}s^3 + \dfrac{RJ+LB}{K}s^2 + \dfrac{RB+K^2}{K}s\right]\theta
    \end{gather}
    
    Now, we can define our transfer function $H$ to be the bit in the square brackets. Writing our $H$ as a matrix gives:
    
    $$HK=  
    \begin{bmatrix}
       {LJ_L}s^3 + [{RJ_l+Lb_l}]s^2 + [{R(b_l)+K^2}]s &&
       {LJ_s}s^3 + [{RJ_s+Lb_s}]s^2 + Rb_ss \\  
       {LJ_s}s^3 + [{RJ_s+Lb_s}]s^2 + Rb_ss &&
       {LJ_L}s^3 + [{RJ_l+Lb_l}]s^2 + [{Rb_l+K^2}]s 
    \end{bmatrix}
$$


\begin{align*}
 H_{11} &= \dfrac{LJ_L}{K}s^3 + \dfrac{RJ_l+Lb_l}{K}s^2 + \dfrac{Rb_l+K^2}{K}s \\
 H_{12} &= \dfrac{LJ_s}{K}s^3 + \dfrac{RJ_s+Lb_s}{K}s^2 + \dfrac{Rb_s}{K}s\\
 H_{21} &= \dfrac{LJ_s}{K}s^3 + \dfrac{RJ_s+Lb_s}{K}s^2 + \dfrac{Rb_s}{K}s\\ 
 H_{22} &= \dfrac{LJ_L}{K}s^3 + \dfrac{RJ_l+Lb_l}{K}s^2 + \dfrac{Rb_l+K^2}{K}s \\
\end{align*}

In all, we have 8 unknown quantities. R is easily measurable, and K is given with the motor. In theory, we should be able to measure L with some degree of precision, but efforts to this end have thus far given questionable results. That aside, I'm going to introduce new coefficients so that when we later do curve fitting, we well be better able to ascertain what coefficients change what.

Let's define
$$
a_1 = \dfrac{LJ_l}{K}\;\;a_2=\dfrac{RJ_L+Lb_l}{K}\;\;a_3=\dfrac{Rb_l+K^2}{K}$$
$$
b_1 = \dfrac{LJ_s}{K}\;\;b_2=\dfrac{RJ_s+Lb_s}{K}\;\;b_3=\dfrac{Rb_s}{K}
$$
This way:

$$
H =  \begin{bmatrix}
       a_1s^3+a_2s^2+a_3s &&
       b_1s^3+b_2s^2+b_3s \\  
       b_1s^3+b_2s^2+b_3s  &&
       a_1s^3+a_2s^2+a_3s 
    \end{bmatrix}
$$
That's nicer to look at. There's an important point in doing this simplification, at first glance, we have 8 unknown quantities. However, looking at $H$, we see there are only 6 values that really make a difference. We're we to try to fit all 8, we'd run into degeneracy issues, and the fit may not terminate, Now, our next step is to somehow find these values empirically.

\section{Experiments}

To understand how our system is going to behave in all circumstances, we need to get all 9 of the above coefficients. We're going to do this by running by driving it with a known voltage, and measuring the resulting motion.

\subsection{Moving forwards in y}

We do this by driving $m_1$ with some positive voltage $V$ (pwm), and $m_2$ with $-V$. We know that we will have $\theta_1 = -\theta_2$
$$
V = H\theta \implies V_1(s) = ((a_l-b_1)s^3 + (a_2-b_2)s^2 + (a_3-b_3)s)\theta
$$
There's an important point in here: if we just run forwards, there are only three values we can extract. Nonetheless, by preforming a curve fit of our angle data to the voltage we supplied, we will be able to determine these differences. 

\subsection{Moving forward in x}

This is done by driving the motors with the same constant voltage (both magnitude and polarity). We know we will have $\theta_1 = \theta_2$, So

$$
V = H\theta \implies V_1(s) = ((a_l+b_1)s^3 + (a_2+b_2)s^2 + (a_3+b_3)s)\theta
$$

By fitting a curve to these sums, and using our fitted values for the sums of the coefficients we can solve for each individual coefficient.

\section{Analytic Solution for F-B and S-S Motion}

These are the cases described in the above experiment we preformed. Moving either side to side, or forward back, the equations relating $V$ and $\theta$ are identical up to the values of the coefficients. Let's define:

$$
V_1(s) = (d_1s^3 + d_2s^2 + d_3s)\theta(s) \iff H^{-1}=\dfrac{1}{d_1s^3+d_2s^2+d3s}
$$
Using partial fraction expansion gives:

$$
H^{-1} = \left[\dfrac{1}{d_3s} - \dfrac{\frac{d_2}{d_3} + \frac{d_1}{d_3}s}{d_1s^2 + d_2s + d_3}\right]
$$
We can now, in a relatively straight forward manner, invert the Laplace transform.

\begin{gather}
 H^{-1}(t) = \dfrac{1}{d_3}\left[1-\exp\left(-\dfrac{d_2}{2d_1}t\right)
 \left(\cosh\left(t\sqrt{(\frac{d_2}{2d_1})^2 -\frac{d_3}{d_1}}\right)
 + 
 \dfrac{\sinh\left(t\sqrt{(\frac{d_2}{2d_1})^2 -\frac{d_3}{d_1}}\right)}{\sqrt{(\frac{d_2}{2d_1})^2 -\frac{d_3}{d_1}}}
 \right)\right]
 \label{anal}
\end{gather}
\subsection{Modes of operation}
There are three possible forms that \eqref{anal} can take depending on the values of $\Delta= (\frac{d_2}{2d_1})^2
-\frac{d_3}{d_1}$ 
\subsection{Critical Damping}
In the unlikely (read: impossible) event that $\Delta=0$, we would have hyperbolic sines or cosines, nor would we have regular sinusoids. \eqref{anal} would reduce to: 
$$
H^{-1}(t) = \dfrac{1}{d_3}\left[1-\exp\left(-\dfrac{d_2}{2d_1}t\right)\right]
$$

\subsection{Over Damping}

Assuming $\Delta>0$, the argument of all exponentials in $\eqref{5}$ are real. And so the behaviour of the system is described by \eqref{anal}. 



\subsection{Under Damping}

Should we have $\Delta<0$, the argument of the hyperbolic sinusoids becomes imaginary. However, since we know $\cosh{ix}=\cos(x)$ and $\sinh{ix}=i\sin(x)$ \eqref{anal} becomes:

\begin{gather*}
     H^{-1}(t) = \dfrac{1}{d_3}\left[1-\exp\left(-\dfrac{d_2}{2d_1}t\right)
 \left(\cos\left(t\sqrt{-(\frac{d_2}{2d_1})^2 +\frac{d_3}{d_1}}\right)
 + 
 \dfrac{\sin\left(t\sqrt{-(\frac{d_2}{2d_1})^2 + \frac{d_3}{d_1}}\right)}{\sqrt{-(\frac{d_2}{2d_1})^2 +\frac{d_3}{d_1}}}
 \right)\right]
\end{gather*}

\subsection{Constant Voltage Case}

Should the motion be preformed with a constant voltage, then the speed is $\dot\theta = H^{-1}V$ (analogously to the integral from of the solution in \eqref{Integral form})

This lets us observe 

\section{Initial Conditions}

Each time we execute a new path we begin with fresh initial conditions. We have to figure out how to accommodate for these. Let's start by repeating the Laplace transforms, but including IC's

\begin{gather*}
   L\dot{I} + RI = V - K\dot\theta \label{I} \rightarrow{} LsI-LI_0 + RI = V - Ks\theta + K\theta_0\\
   I = \dfrac{V-Ks\theta}{Ls+R} + \dfrac{LI_0+K\theta_0}{Ls+R}
\end{gather*}
For the torque equation we have:

\begin{gather*}
 \tau = J\ddot\theta+B\dot\theta \rightarrow Js^2\theta - sJ\theta_0 - J\dot\theta_0 + Bs\theta - B\theta_0\\
 \tau(s) = (Js^2+Bs)\theta - [(Js+B)\theta_0+J\dot\theta_0] 
\end{gather*}
Putting these together:
\begin{gather*}
     \tau = KI = K[\dfrac{V-Ks\theta}{Ls+R} + \dfrac{LI_0+K\theta_0}{Ls+R}]=(Js^2+Bs)\theta - [(Js+B)\theta_0+J\dot\theta_0] \\
     V = \left[\dfrac{Ls+R}{K}(Js^2+Bs)+Ks\right]\theta -\left[\dfrac{Ls+R}{K}(Js+B)+K\right]\theta_0-\dfrac{Ls+R}{K}J\dot\theta_0-LI_0\\
\end{gather*}
We have four terms here. The first is just our $H$ matrix from before. The next term has the same coefficients as H, but is one order lower in s. Call this matrix $\Tilde{H}$. So:

\begin{gather}
    V = H\theta - \Tilde{H}\theta_0 -\dfrac{Ls+R}{K}J\dot\theta_0-LI_0 \label{IC}
\end{gather}

However, in we can see in \eqref{IC} that all the terms involving initial conditions are proportional to non-negative powers of $s$. This mean that the inverse Laplace transform of these terms will just result in various orders of derivatives of the Dirac Delta function. These Dirac's aren't every integrated over - only the multiplication of $H(s)\theta(s)$ leads to integration in the time domain.

\begin{gather*}
    V(t) = \int_0^tV(t-\tau)H(\tau)d\tau+\mathcal{L}^{-1}\{\text{Initial condition stuff}\}
\end{gather*}

The initial condition stuff is zero for all non-zero time so we simply have:

\begin{gather}
    V(t) = \int_0^t\theta(t-\tau)H(\tau)d\tau \label{Integral form}
\end{gather}

\begin{gather}
    V = H(\dddot\theta_d, \ddot\theta_d, \dot\theta_d, t) + K_p(\theta - \theta_d) + K_d(\dot\theta - \dot\theta_d)
\end{gather}

\end{document}





