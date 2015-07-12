##Bar graph

```
(letrec indexedzip_
  (\(acc xs)
    (case xs
      ([x | xx] [[acc x] | (indexedzip_ (+ acc 1) xx)])
      (_ [])))
(let indexedzip (\xs (indexedzip_ 0 xs))
(let [x0 y0 sep] [28 28 20]
(let xaxis (rect 'black' (- x0 8) (+ y0 400) 800 5)
(let yaxis (rect 'black' (- x0 8) y0 5 400)
(letrec range
  (\(b t)
    (if (< b t)
      [b | (range (+ b 1) t)]
      [t]))
(let data (map (\a (sin a)) (map (\b (* b 0.25)) (range 0 40)))
(let bars
  (map
    (\[i j] (rect 'lightblue' (+ x0 (* i sep)) (+ y0 (- 400 (* 100 j))) 20 (* 100 j)))
    (indexedzip  data))
  (svg  [xaxis yaxis | bars])))))))))
```

##Graph (nodes and edges)

```
(let node (\[x y] (circle 'lightblue' x y 20))
(let edge (\[[x y] [i j]] (line 'lightgreen' 5 x y i j))
(letrec genpairs
  (\xs
    (case xs
      ([x y | xx] [[x y] | (append (genpairs  (cons x xx)) (genpairs  (cons y xx)))])
      ([x] [])
      ([] [])))
(let pts [[200 50] [400 50] [100 223] [200 389] [400 391] [500 223]]
(let nodes (map node pts)
(let pairs (genpairs  pts)
(let edges (map edge pairs)
  (svg  (append edges nodes)))))))))
```

##Fractal Tree

Generate the branches by starting with a child list, then recursively generating children until number of steps is depleted.

```
; A fractal tree
(defrec mod (\(x m) (if (< x m) x (mod (- x m) m))))
(def nsin (\n (if (< n (/ 3.14159 2)) (sin n) (cos (mod n (/ 3.14159 2))))))
(def ncos (\n (if (< n (/ 3.14159 2)) (cos n) (sin (mod n (/ 3.14159 2))))))
(def [initwd initlen] [10! 150!])
(def [steps stepslider] (hSlider true 20! 420! 550! 3! 8! 'Steps ' 4))
(def [bendn bendnslider] (hSlider true 20! 420! 580! 1! 8! 'Bend ' 1))
(def initangle (/ 3.14159! 2!))
(def bend (/ 3.14159! bendn))
(defrec exp (\(base pow)
  (if (< pow 1) 1 (* base (exp base (- pow 1))))))
(def mkleftx (\(stepnum theta px) 
  (- px (* (/ initlen stepnum) (ncos (+ theta (* (exp 0.5 stepnum) bend)))))))
(def mkrightx (\(stepnum theta px)
  (+ px (* (/ initlen stepnum) (ncos (- theta (* (exp 0.5 stepnum) bend)))))))
(def mklefty (\(stepnum theta py)
  (- py (* (/ initlen stepnum) (nsin (+ theta (* (exp 0.5 stepnum) bend)))))))
(def mkrighty (\(stepnum theta py)
  (- py (* (/ initlen stepnum) (nsin (- theta (* (exp 0.5 stepnum) bend)))))))
(defrec genchildren (\(stepnum maxstep theta px2 py2) 
  (if (< maxstep stepnum) 
    [] 
    (append 
      [ (line 'black' (/ initwd stepnum) px2 py2 
          (mkleftx stepnum theta px2)
          (mklefty stepnum theta py2))
        (line 'black' (/ initwd stepnum) px2 py2
          (mkrightx stepnum theta px2)
          (mkrighty stepnum theta py2))]
      (append
        (genchildren (+ stepnum 1) maxstep (+ theta (* (exp 0.5 stepnum) bend))
          (mkleftx stepnum theta px2)
          (mklefty stepnum theta py2))
        (genchildren (+ stepnum 1) maxstep (- theta (* (exp 0.5 stepnum) bend))
          (mkrightx stepnum theta px2)
          (mkrighty stepnum theta py2)))))))
(def trunk (line 'black' initwd 210 400 210 250))
(def branches (genchildren 2 steps initangle 210 250))
(svg (concat [ [ trunk | branches ] bendnslider stepslider]))
```

##Solar System
A representation of the solar system that can be advanced through time, where the relative positions of the planets in their orbits are correct as well as the relative radii of the orbits themselves.

```
; Visualization of the solar system 
(def aupx 12)
(def [ox oy] [200 400])
; Relative radii of the planet orbits, in au
(def [ merorb venorb earorb marorb juporb satorb uraorb neporb ] 
     [ 0.387! 0.723! 1! 1.524! 5.203! 9.539! 19.18! 30.06! ]
)
; Relative orbital period to the Earth
(def [ meryr venyr earyr maryr jupyr satyr urayr nepyr ]
     [ 0.2409! 0.616! 1! 1.9! 12! 29.5! 84! 165! ]
)
; Function to place a body
(def planet (\(color orb yr radius)
  (\t (circle color  (+ ox (* aupx (* orb (cos (* t (/ 6.28318 yr))))))
                       (+ oy (* aupx (* orb (sin (* t (/ -6.28318 yr))))))
                       radius))))
; Visual for each body
; Each takes a time to be displayed at
(def sun (circle 'yellow' ox oy 10))
(def mercury (planet 'lightred'   merorb meryr 4))
(def venus   (planet 'orange'     venorb venyr 5))
(def earth   (planet 'green'      earorb earyr 5))
(def mars    (planet 'red'        marorb maryr 4))
(def jupiter (planet 'brown'      juporb jupyr 6))
(def saturn  (planet 'sandybrown' satorb satyr 6))
(def uranus  (planet 'blue'       uraorb urayr 6))
(def neptune (planet 'darkblue'   neporb nepyr 6))
; Visual for the rings
(def rings (reverse (map (\orb (ring 'lightgrey' 2! ox oy (* aupx orb)))
                [ merorb venorb earorb marorb juporb satorb uraorb neporb ])))
(def [time timeslider] (hSlider true 20! 600! 20! 1! 1000! 'Day ' 1))
(def rev (\(x f) (f x)))
(def planets (map (rev (/ time 365)) [mercury venus earth mars jupiter saturn uranus neptune]))
(svg (concat [ rings [sun | planets] timeslider ]))
```

##2D Slider
A two dimensional slider in a similar style to the slider that already exists.

```
(def xySlider_
  (\(dropBall roundInt xStart xEnd yStart yEnd minx maxx miny maxy xcaption ycaption curx cury)
    (def [rCorner wEdge rBall] [4! 3! 10!])
    (def [xDiff yDiff xValDiff yValDiff] [(- xEnd xStart) (- yEnd yStart) (- maxx minx) (- maxy miny)])
    (def ballx (+ xStart (* xDiff (/ (- curx minx) xValDiff))))
    (def bally (+ yStart (* yDiff (/ (- cury miny) yValDiff))))
    (def ballx_ (clamp xStart xEnd ballx))
    (def bally_ (clamp yStart yEnd bally))
    (def rball_ (if dropBall (if (< maxx curx) 0 rBall) rBall))
    (def rball__ (if dropBall (if (< maxy cury) 0 rball_) rBall))
    (def xval
      (def xval_ (clamp minx maxx curx))
      (if roundInt (round xval_) xval_))
    (def yval
      (def yval_ (clamp miny maxy cury))
      (if roundInt (round yval_) yval_))
    (def shapes
      [ (line 'black' wEdge xStart yStart xEnd yStart)
        (line 'black' wEdge xStart yStart xStart yEnd)
        (line 'black' wEdge xStart yEnd xEnd yEnd)
        (line 'black' wEdge xEnd yStart xEnd yEnd)
        (circle 'black' xStart yStart rCorner)
        (circle 'black' xStart yEnd rCorner)
        (circle 'black' xEnd yStart rCorner)
        (circle 'black' xEnd yEnd rCorner)
        (circle 'black' ballx_ bally_ rball__)
        (text (- (+ xStart (/ xDiff 2)) 40) (+ yEnd 20) (+ xcaption (toString xval)))
        (text (+ xEnd 10) (+ yStart (/ yDiff 2)) (+ ycaption (toString yval))) ])
  [ [ xval yval ] shapes ]))
(def xySlider (xySlider_ false))
(def [ [ a b ] slider ] (xySlider false 20! 420! 20! 420! 0! 100! 0! 100! 'X Axis: ' 'Y Axis: ' 20 20))
(svg slider)
```

##Color Picker with 2d Slider

Because color theory is subtle and complex, however compelling, I'm going to do two separate 2D sliders - one for RG and the other for BA and see how that looks.

```
; The slider
(def xySlider_
  (\(dropBall roundInt xStart xEnd yStart yEnd minx maxx miny maxy xcaption ycaption curx cury)
    (def [rCorner wEdge rBall] [4! 3! 10!])
    (def [xDiff yDiff xValDiff yValDiff] [(- xEnd xStart) (- yEnd yStart) (- maxx minx) (- maxy miny)])
    (def ballx (+ xStart (* xDiff (/ (- curx minx) xValDiff))))
    (def bally (+ yStart (* yDiff (/ (- cury miny) yValDiff))))
    (def ballx_ (clamp xStart xEnd ballx))
    (def bally_ (clamp yStart yEnd bally))
    (def rball_ (if dropBall (if (< maxx curx) 0 rBall) rBall))
    (def rball__ (if dropBall (if (< maxy cury) 0 rball_) rBall))
    (def xval
      (def xval_ (clamp minx maxx curx))
      (if roundInt (round xval_) xval_))
    (def yval
      (def yval_ (clamp miny maxy cury))
      (if roundInt (round yval_) yval_))
    (def shapes
      [ (line 'black' wEdge xStart yStart xEnd yStart)
        (line 'black' wEdge xStart yStart xStart yEnd)
        (line 'black' wEdge xStart yEnd xEnd yEnd)
        (line 'black' wEdge xEnd yStart xEnd yEnd)
        (circle 'black' xStart yStart rCorner)
        (circle 'black' xStart yEnd rCorner)
        (circle 'black' xEnd yStart rCorner)
        (circle 'black' xEnd yEnd rCorner)
        (circle 'black' ballx_ bally_ rball__)
        (text (- (+ xStart (/ xDiff 2)) 40) (+ yEnd 20) (+ xcaption (toString xval)))
        (text (+ xEnd 10) (+ yStart (/ yDiff 2)) (+ ycaption (toString yval))) ])
  [ [ xval yval ] shapes ]))
(def xySlider (xySlider_ false))
;
; RG and BA sliders
(def [ [ red green ] rgSlider ] (xySlider true 20! 200! 20! 200! 0! 255! 0! 255! 'Red: ' 'Green: ' 78 215))
(def [ [ blue alpha ] baSlider ] (xySlider true 20! 200! 240! 430! 0! 255! 0! 255! 'Blue: ' 'Alpha: ' 220 100))
(def colorBall (circle [ red green blue alpha ] 400 220 100!))
(svg (cons colorBall (append rgSlider baSlider)))
```

##Phasic Things
Waves, rotation, and phase are all things that are difficult to capture in a normal graphical editor and even more difficult to manipulate. This could provide some compelling examples for both normal direct manipulation as well as sliders.

Possible examples:
* A boat on a sea, waves and boat's position on top is determined using trig
* Interference pattern, as visualized by changing alpha of a radial or linear gradient. Frequency of emission sources could be manipulated with sliders.
* Rotation of a cube, with shading easily determined by value of one rotational parameter (unidirectional light)
* Turning a page in a book (would require some math for the curve at the edge of the page)
* A bouncing ball with path shown
* A hanging spring or a swinging pendulum, where the size of the block determines the 'mass' and the resulting period/aplitude

##Relate and introduceParameter
Two example use cases:
* User places three otherwise identical boxes in a near-horizontal line, then asks the program to 'relate' them. Ideally, the program should deduce that the only varying parameters (the x and y positions of the boxes) can be modeled (like with a best fit line) by introducing a few parameters. Namely, by introducing a named constant for the y position that all three boxes share and by introducing two named constants for the x position that are a part of a linear function (an x0 and a separation, effectively).
* User creates a larger, more complicated shape/graphic by building it up from smaller polygons (such as using triangles and rectangles to make the city skyline in the Active Trans Logo example). These are ultimately inteded to be one shape, so the user then 'groups' the shapes and fuses them into one polyline. The user then selects the points that makes up the skyline (just the top ones) and asks the program to 'relate' them. Ideally, the program should output an option where a parameter was introduced into the y values of the points, where the other points are assigned as offsets to the 'base' point. Then, if the user picks this choice, they would be able to manipulate the skyline as in the example.

###introduceParameter
Perhaps in the case when the Vals passed to the 'relate' function have exactly the same structure, the algorithm could consist of successive best fits on each of the constants at a leaf of the Val. So, in the case of the boxes, there are two parameters on which fits are done, the x and the y (the other attributes, being identical, presumably are already related by a named constant). The fits done are in increasing complexity, from linear to quadratic, to more exotic ones like logarithmic and fourier. The r^2 values for these could then act as a ranking to determine which ones are to be higher ranked than the others.

These fits could also be compared to a few special ones that human users will tend to place along, like vertical and horizontal lines. grids and circles might be hard to detect and parameterize, but they are also common placement patterns that would be good to detect.

Work in progress:

```
\begin{LaTeX}

If $V_i \sim V_n$ for $ i \in [0,m], n \in [0,m] $,
$relate(V_0, ..., V_m) = \{ V^0, ..., V^K \}$ where 

$V^N \in introduceParameter(n_0, ..., n_m) $ for $ n \in V_i $.

$n$ is meant to be a constant in the input $V_i$ Vals. The arguments to $introduceParameter()$ are meant to be the constants that correspond to the same 'structural location' in all of the input $V_i$. $introduceParameter()$ returns a set of $V^N$, each of which is a set of replacement Vals that correspond to one possible parameter introduction. So, that's where any fitting might happen. The parameter introductions potentially result in new expressions $e_i$ that replace the previous $n_i$ in the $V_i$. The options that are then shown are all the substitutions that correspond to the elements of the $V^N$.

Could this be expressed with our current substitution notation? To complete this specification, I think I need to look at exactly how we did things in the technical paper.

\end{LaTeX}
```