<center>

# Доказательство с нулевым разглашением на основе задачи о 3-х раскрашиваемом графе
    (Zero knowledge 3-colorability with RSA)

### ННГУ им. Н. И. Лобачевского
    N. I. Lobachevsky State University of Nizhny Novgorod
### Институт информационных технологий, математики и механики
    Institute of information technology, mathematics and mechanics
    
</center>

## Теория
Задача раскраски графа в $k$ цветов при любом фиксированном $k \geq 3$ является NP-полной, следовательно для задачи раскраски графа существует доказательство с нулевым разглашением. Рассмотрим протокол, но с применением криптографического алгоритма RSA. Будем учитывать, что любая из сторон (Prover или Verifier) может обмануть другую.

Рассмотрим граф $G=(V,E)$, раскрашиваемый тремя цветами, где $V = \{v_1,v_2,\dots,v_k\}.$ Пусть Доказывающий (Prover) знает граф и его раскраску:


| $v_1$    | $v_2$    | $\dots$ | $v_k$    |
|----------|----------|---------|----------|
| $c(v_1)$ | $c(v_2)$ | $\dots$ | $c(v_k)$ |

где $c(v_i) \in \{R,G,B\}, i = \overline{1,k}.$

Верификатор (Verifier), в свою очередь, знает только сам граф. Prover хочет доказать Verifier'y то, что знает правильную раскраску графа, не предъявляя саму раскраску.

Протокол состоит в следующем:
1. Prover выбирает случайную перестановку $\pi$ из $\{ R,G,B \}$ и формирует новую таблицу $\widetilde{T}$ раскраски графа:  

| $v_1$         | $v_2$         | $\dots$ | $v_k$         |
|---------------|---------------|---------|---------------|
| $\pi(c(v_1))$ | $\pi(c(v_2))$ | $\dots$ | $\pi(c(v_k))$ |
  
Для каждой вершины $v \in V$ Prover генерирует большое случайное число $r_v$ и преобразовывает по следующему правилу:

- Если вершина $v$ в таблице $\widetilde{T}$ окрашена в $R,$ то меняем последние два бита в $r_v$ на $00.$
- Если вершина $v$ в таблице $\widetilde{T}$ окрашена в $G,$ то меняем последние два бита в $r_v$ на $01.$
- Если вершина $v$ в таблице $\widetilde{T}$ окрашена в $B,$ то меняем последние два бита в $r_v$ на $10.$

Для каждой вершины $v \in V$ Prover формирует параметры для алгоритма \texttt{RSA}:

Генерирует простые числа $p_v$ и $q_v,$ вычисляет $n_v = p_v \cdot q_v$ и функцию Эйлера $$\varphi(n_v)=(p_v-1)(q_v-1).$$ Затем выбирает такое $e_v,$ что $$1\le e_v \le \varphi(n_v)\text{ и }\gcd(e_v,\varphi(n_v))=1.$$
Вычисляет число $d_v,$ мультипликативно обратное к числу $e_v$ по модулю $\varphi(n_v),$ то есть удовлетворяющее сравнению: $$d_v \cdot e_v \equiv 1 \mathrm{mod}(\varphi(n_v)).$$
Вычисляет число: $$m_v=r_v^{e_v} \mathrm{mod} n_v,$$ здесь число $r_v$ с измененными двумя последними битами по описанному выше правилу. 

Затем формирует и посылает следующую таблицу Verifier'y:

| $v_1$         | $v_2$         | $\dots$ | $v_k$         |
|---------------|---------------|---------|---------------|
| $n_1,e_1,m_1$ | $n_2,e_2,m_2$ | $\dots$ | $n_k,e_k,m_k$ |

2. Verifier случайно выбирает ребро $(u,w)\in E$ и сообщает его Prover'y.
3. В ответ Prover присылает Verifier'y числа $d_{u}$ и $d_{w},$ которые соответствуют вершинам выбранного ребра. После чего Verifier вычисляет:
$$\widetilde{m}_{u}=m_{u}^{d_{u}}\,\mathrm{mod}\,(n_{u}) = r_{u} \text{ и } \widetilde{m}_{w} = m_{w}^{d_{w}}\,\mathrm{mod}\,(n_{w}) = r_{w},$$
затем сравнивает два последних бита в этих числах. При правильной раскраске два последних бита должны быть различны. Если они получились одинаковыми, то Prover попытался обмануть (не знает раскраски: покрасил вершины $u,w$ в один цвет). Если различны, то все шаги повторяем процедуру нужное количество раз.

Выясним, сколько раз нужно повторять процедуру, чтобы обеспечить желаемую достоверность. Пусть $p$ вероятность того, что Prover не обманул Verifier'a и знает правильную раскраску в $3$ цвета. Предположим, Prover решил обмануть и покрасил две смежные вершины в один цвет. Тогда вероятность того, что Verifier выберет это ребро равна $\frac{1}{|E|}$ и Prover будет разоблачен, значит вероятность неразоблачения равна $1 - \frac{1}{|E|}.$ Следовательно, за $k$ шагов вероятность неразоблачения равна $(1-\frac{1}{|E|})^{k}.$ Тогда количество шагов $k,$ которое необходимо достижения достоверности $p,$ удовлетворяет неравенству: $$\left(1-\frac{1}{|E|}\right)^k < 1-p$$

Решив его, найдем, что: $$\frac{\ln\left(1-p\right)}{\ln\left(1-\dfrac{1}{|E|}\right)} < k.$$

Используя неравенство $\left(1-\frac{1}{x}\right)^{a\cdot x} < e^{-a}$ и взяв $k = [\mu\cdot|E|]$ (целая часть) получаем:
$$\left(1-\frac{1}{|E|}\right)^{\mu\cdot|E|}<e^{-\mu}.$$

Если хотим, чтобы $e^{-\mu} < 1-p,$ то коэффициент $\mu$ должен удовлетворять неравенству: 
$$\mu > \ln\left(\frac{1}{1-p}\right).$$


Приведем значения $\mu$ и $k$ для некоторых значений $p$:
- $p = 0.90,$ то $\mu > 2.31,$ поэтому число $k > 2.31\cdot|E|.$
- $p = 0.95,$ то $\mu > 2.99,$ поэтому число $k > 2.99\cdot|E|.$
- $p = 0.99,$ то $\mu > 4.6,$ поэтому число $k > 4.6\cdot|E|.$

Очевидно, чем больше достоверность $p$, тем больше должен быть коэффициент $\mu$ и тем больше число шагов необходимо сделать. Кроме того, заметим, чем больше ребер в графе, тем больше шагов надо выполнить для достижения желаемой достоверности.
