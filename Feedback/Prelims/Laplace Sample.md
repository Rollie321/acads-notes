
### Problem 1:
Find the inverse Laplace of the following equation:
$$ \frac{2s - 4}{s^2 - 4s + 13}$$

#### Solution:
##### 1: Convert the denominator into a sum of perfect square
Denominator: 
$$ s^2 - 4s + 13 $$
###### 1.1 Find the constant to be added based on the coefficient of middle (s)
$$ s^2 - \underline{4}s + 13 $$
###### 1.2 take its half and square it:
$$ \frac{4}{2} = 2$$
$$ 2^2 = 4$$
$$ additional factor = 4 $$
###### 1.3 Add and subtract it to the original equation
$$ s^2 - 4s + 13 - 4 + 4$$
$$ s^2 - 4s + 13 \boxed{- 4 + 4}$$
$$ s^2 - 4s + 4 + 13 - 4$$
$$ s^2 - 4s + 4 + 9$$
$$Since\ the\ first\ three\ terms\ (s^2 - 4s + 4)\ and\ 9\ are\ now\ factorable$$
$$ s^2 - 4s + 4 = (s - 2)(s - 2)$$
$$ (s - 2)(s - 2) = \boxed{(s-2)^2}$$
$$ 9 = \boxed{3^2}$$
Now we can rewrite the denominator:
$$ s^2 - 4s + 13 = \boxed{(s-2)^2 + 3^2} $$
##### 2. Rewrite the new equation:
Replace the denominator that we created 
$$ \frac{2s - 4}{s^2 - 4s + 13} = \frac{2s - 4}{(s^2 - 2)^2 + 3^2}$$
Factor out the 2 from numerator
$$ \frac{2(s- 2)}{(s-2)^2+3^2} $$
Move it outside the fraction (*wala lang pampaganda lang*)

$$ 2\frac{s-2}{(s-2)^2 + 3^2}$$
Take note of these theorems:
$$ \mathcal{L} \left\{cos\ \omega t\right\} = \frac{s}{s^2 + \omega^2} $$
$$ \mathcal{L} \left\{ e^{-at} \right\} = \frac{1}{s+a}$$
Now we can take the inverse Laplace of the equation
$$ \mathcal{L}^{-1} \left\{ 2\frac{s-2}{(s-2)^2 + 3^2} \right\}$$
Take out the 2
$$ 2\ \mathcal{L}^{-1} \left\{\frac{s-2}{(s-2)^2 + 3^2} \right\}$$
Apply the theorems

$$ 2\ \mathcal{L}^{-1} \left\{\frac{s-2}{(s-2)^2 + 3^2} \right\} = 2 e^{2t}cos\ 3t$$
$$2 e^{2t}cos\ 3t$$
Write the answer as a proper function
$$ \boxed{2e^{2t}cos\ 3t\ u(t)}$$