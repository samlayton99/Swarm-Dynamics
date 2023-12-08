# all-the-buzz


\begin{document}

\title{Flight of the Bumblebees: \\ An Attraction-based Simulation}
\author{A. de la Bruere, J. Gertsch, S. Layton, M. Mella, W. Stoddard}

%% comment out next command to put today's date after names of group members, or put a desired day in the parethesis
% \date{\today}

\maketitle

\begin{abstract}
We model the motion of agents navigating towards a target destination in the face of distracting attractors. Specifically, in this paper we model the flight of bumblebees returning to their hive through a field of flowers. Our resulting model is a first-order dynamical system of independent agents as they face distractions against a primary objective. This research fits into a larger body of work focused on the study of animal movement and migration, typically modeled using stochastic or partial differential equations. Our research is unique in its simulation and treatment of distractions along the path of motion. The implications of this work are not only relevant to animal movement, but also contain potential applications to behavioral economic models. 
\end{abstract}

\section{Background/Motivation}
The goal of this model is to explain the behavior of free agents progressing toward a destination while navigating through a field of various distractions. We chose to model the flight of bees on their way back to a hive (the destination) through a field of flowers (the distractions). We began deriving our model using principles from gravity \cite{Vol4}, where attraction to any destination or distraction was influenced by both proximity (distance) and preferences (a proxy for mass).

Originally, we considered this simulating this problem in the context of customers in a grocery store. Customers were tasked with locating a specific item while facing distractions such as ads, sales, and product samples. Similarly, this type of model could represent tourists in a city navigating to a famous landmark along a street full of tourist traps. However, a fixed store or city layout was more restrictive than we cared for. In order to give sufficient attention to the movement and behavior of an agent faced with various distractions, we abstracted this idea to bees in an unencumbered field. Studying the model we created can give intuition on the influence of proximity and preference on the motion of an agent through space that can be generalized to more specific settings and spaces.

Groups such as grocery retailers and city planners have incentive to care about modeling the movement of agents through space. Retailers understanding this problem could arrange a store optimally to make shopping trips more efficient and also to maximize revenue with clever placement of goods. City planners could optimize traffic flow and the construction of new buildings to account for hotspots in their city.

Modeling the motion of animals has merit in its own right. There is a large body of research focused on animal movement and migration, published in journals focused on ecology, physics, applied statistics, and theoretical biology. \cite{Preisler, Russell, Wang, Birnir}. Researchers employ a variety of techniques based on differential equations, including stochastic and partial differential equations \cite{Preisler, Wang}. Some even implement statistical methods such as likelihood estimation and machine learning algorithms to supplement their models \cite{Michelot, Wijey}. Oftentimes, inspiration for modeling animal movement is gained through direct observation, whether through monitoring ant colonies in a nest or radio-collaring elk in a protected forest \cite{Preisler, Russell}.

While we did not have the resources necessary to perform costly observations or the tools to incorporate data into our differential equations, our model is still novel due its focus on attractors in the animal’s path. We tailor the model to account for the bee preferences, flower characteristics, distance from attractors, and an objective to eventually return to the hive. Some research focuses on the migration of fish or birds \cite{Birnir}, but our model is again unique in its focus on the attractors in a specific path. The value of our work is that it yields results applicable to modeling the movement of animals as well as the movement of humans with real economic implications. 

\section{Modeling}

\noindent \textbf{Primitive Model}\\

As a starting point for modeling the attraction of bees to flowers in a field, we took inspiration from the law of gravity. This law states that the gravitational force exerted on a body is directly proportional to the sum of forces exerted by all other bodies in the universe, where the individual force exerted by any given body is directly proportional to its mass and inversely proportional to the square of the distance between the two objects \cite{Vol4}. We surmised that in our model, the correspondence of a bee's preferences and a flower's unique attributes would play the role of mass in attraction (i.e., the attraction force would be directly proportional to a score of preference correspondence). Each flowers has a vector of attributes to represent features such as size, color, and scent. Each bee has a vector of preferences corresponding to these attributes. By evaluating the norm between these vectors, we find the attraction force of each flower to the bees. Similarly, we can extend this to the case of customers in a store, where each person has a unique set of preferences that may or may not align with the goods they encounter. To extend our model, a retailer could use statistical measures to attempt an accurate estimation of customer preferences.

We note that bees do not zoom exponentially fast towards flowers that catch their attention, thus it was not realistic for the force of attraction to decrease as a function of distance. Observing that bees in nature often achieve a maximum flight velocity that they maintain throughout their travel, we decided to model a particular flower's effect on bee velocity using a logistic function, so that its velocity would be bounded between 0 and some $\bold{v}_{max}$. We thus modified the gravitational model to derive the following model of bee position $\bold{x_b}$ as a function of the preference correspondence, $\bold{p_f}$ and the position $\bold{x_f}$ of the attracting flower:

\begin{align}
    G(\bold{x_b},\bold{p_f}) = \frac{\alpha\bold{p_f}}{\lVert \bold{x_f} - \bold{x_b} \rVert^2} \\
    \dot{\bold{x}_{\bold{b}}} = \frac{\bold{v}_{max}}{1+e^{-G(\bold{x_b},\bold{p_f})}}\frac{\bold{x_f}-\bold{x_b}}{\lVert \bold{x_f} - \bold{x_b} \rVert}
\end{align}

To test the efficacy of this model, we created a system of one flower placed at the origin and one bee with initial position $\bold{x_b}=\begin{bmatrix} 5 \\ 2 \end{bmatrix}$. We then generated a numerical solution to the corresponding initial value problem. In this simplistic model, the bee makes a proper beeline to the flower as expected, but in a linear, robotic way that does not represent the trajectory of bees in nature. The exact trajectory taken is shown in the left plot of Figure \ref{fig:PrimitiveModel}.

\begin{figure}[h]
\begin{minipage}[h]{0.47\linewidth}
\begin{center}
\includegraphics[width=\textwidth]{single_attractor.png}
\end{center}
\end{minipage}
\begin{minipage}[h]{0.47\linewidth}
\begin{center}
\includegraphics[width=\textwidth]{equal_attractors.png}
\end{center}
\end{minipage}
\caption{Left: The trajectory of a single bee flying from its initial position to the sole attracting flower.
Right: A bee starting at its initial position flies and then stops at an unnatural equilibrium--a point without flowers.}
\label{fig:PrimitiveModel}
\end{figure}

In an attempt to introduce curvature in the bee's movement, we added several more flowers and generated another simulation, which is shown in the right plot of Figure \ref{fig:PrimitiveModel}.

As can be seen, some curvature was introduced. However, after flying in the general direction of the flowers, the bee uncharacteristically settled down and hovered in time, failing to arrive at any flower in the simulation. After some experimentation, we determined that this spot was actually an unforeseen equilibrium because the sum of the forces of attraction was zero at this position. This unusual result revealed that our assumption that the bee's velocity would be determined by a pure sum of forces of attraction was flawed, introducing unreasonable equilibria into our model.\\

\noindent \textbf{Intermediate Model}\\

Taking into account the unrealistic linear motion and non-flower equilibrium discovered in the previous model, we updated our model in two important ways. First, to produce movement more characteristic of a bee's flight observed in nature, we introduced a zagging motion to the bee's trajectory. We did so by adding a perturbation to the bee's trajectory in the form of a sine wave perpendicular to the direction of the bee's given velocity as a function of time.

Second, to eliminate the ``decision paralysis" effect that we observed in the initial model (the unnatural equilibria where the sum of the forces of attraction of all flowers was zero), we tweaked the model so that the bee always ultimately chooses one flower amidst many attractive flowers. In particular, we set the attraction force of all flowers but the flower with the maximum preference correspondence equal to zero.

These two changes together led to the following updated differential equation for position:

\begin{align}
    \dot{\bold{x}_{\bold{b}}} = \sum_{\text{f in Field}} ((\mathds{1}_{\bold{p_f} = \textit{max } \bold{p}}) \frac{\bold{v}_{max}}{1+e^{-G(\bold{x_b},\bold{p_f})}}\frac{\bold{x_f}-\bold{x_b}}{\lVert \bold{x_f} - \bold{x_b} \rVert}) + A\dot{\bold{x}}_{\bold{b}_{prev}}\sin{\omega t}
\end{align}

where $A$ and $\omega$ are parameters governing the amplitude and frequency of the zag motion, respectively. The results of this intermediate model produced significantly improved models of bee flight toward an attracting flower, as illustrated in Figure \ref{fig:BuzzSelection}.

\begin{figure}[h]
\begin{center} %Put your images in a figure like this
\includegraphics[height=2.0in]{buzz_selection.png}. % Better to make them pdfs than png or gif or jpeg
\end{center}
\caption{A simulation of a bee's trajectory amidst various flowers. Adding natural zag and a selection mechanism to the model resulted in more natural trajectories.}
\label{fig:BuzzSelection}
\end{figure}
As reflected in the plot of the bee's trajectory under the intermediate model, introducing a sine perturbation to the bee's trajectory resulted in the familiar ambling, casual pattern of a bee in flight. In addition, experimentation gave evidence that this model was free from unexpected equilibria (in other words, there were no points at which a bee stopped at a flowerless location).\\

\noindent \textbf{Final Model Specifications}\\

While our intermediate model succeeded in producing bee-like movement, it suffered from some limitations as time went on. In particular, once a bee landed at a flower, nothing in our model allowed it to move to any other flowers (i.e., preference was too static). In addition, our model lacked a mechanism for vision; for example, a bee would theoretically pass by a highly attractive flower next to it for a marginally more attractive one significantly farther away.

Our final model more consistently matches environmental and behavioral interpretations. First, we introduce a pervasive attraction to the general goal of returning to a beehive. We also introduce a time limit for agents at individual flowers, after which the flower no longer has any attractive force on that particular bee. This represents how a bee in nature moves to another flower once it has consumed a satifactory amount of nectar. We also adapt our mechanism of flower attraction to be governed by a ``vision" factor. This vision factor attracts agents to flowers at a Gaussian rate, allowing flowers to have stronger effects on bees in a given range, and approximately zero effect on flowers ``out of sight".

The parameters and equations governing motion in this updated model are listed in Table 1 below. In what follows, $n$ is the total number of attractors, while $K$ is the total number of bees.
\begin{table}[h!]
\centering
\renewcommand{\arraystretch}{1.25} % Adjusts row height
\captionsetup{belowskip=12pt,aboveskip=4pt} 
\begin{tabular}{|c|l|}
\hline
\textbf{Parameter} & \textbf{Description} \\ \hline
\( h \in \mathbb{R}^{2}\) & Hive position \\ \hline
\( f_i \in \mathbb{R}^{2}\) & Attractor position (flower) $i \in \{1,\ldots,n \}$ \\ \hline
\( p_k \in \mathbb{R}^{L}\) & Preference vector of agent k $\in \{1,\ldots,K \}$ \\ \hline
\( q_i \in \mathbb{R}^{L} \) & Preference vector of attractor $i$ \\ \hline
\( \alpha \) & Vision distance \\ \hline
\( b \) & Minimum baseline attraction \\ \hline
\( T_i \) & Nectar value (time at attractor) \\ \hline
\( V \) & Max speed \\ \hline
\( \xi \) & Flight acceleration factor \\ \hline
\end{tabular}
\caption{Parameters and their Descriptions}
\label{table:parameters}
\end{table}
The model presented herein simulates bee movement, focusing on the interaction between bees and flowers. The key variable, \(x_k \in \mathbb{R}^2\), represents the bee's position. The model integrates various environmental parameters as detailed in Table \ref{table:parameters} in order to describe the complex dynamics of bee navigation as follows:
\begin{align}
    G_i(x_k,p_k) &= \left(e^{-\alpha||f_i - x_k||^2} + b\right) \left(\frac{1}{1 + ||q_i - p_k||^{2}}\right)\left(\frac{f_i - x_k}{||f_i - x_k||}\right)\\
    B (x_k,t) &= \mathds{1}(G_{1, \dots, n})\left(\frac{t}{1+||h - x_k||}\right)\left(\frac{h - x_k}{||h - x_k||}\right)\\
    \gamma_i(x_k,t) &= \mathds{1}_{[f_i, T_i]}(x_k,t) \\
    \gamma^{T} &= [\gamma_1, \gamma_2, \ldots, \gamma_n]\\
    G &= [G_1, G_2, \ldots, G_n]^{T}\\
    \dot{x_k} &= V \frac{\gamma^{T} G + B}{\xi + ||\gamma^{T} G + B||}
\end{align}
The attraction of bee \(k\) to flower \(i\) is modeled by \(G_i(x_k, p_k)\), as defined in Equation \(4\). This function of the bee's position and preferences comprises a Gaussian-like vision factor, a preference-matching component, and a unit vector in the direction of the flower. We also factor in a persistent pull towards the hive, as given by \(B(x_k, t)\) in Equation \(5\). For this equation, the indicator is activated when the sum of the \(G_i\) is greater than one, the middle expression represents an attraction to the hive that grows over time, and finally a unit direction of attraction. It can be shown that 

\begin{equation}
    ||\gamma^TG|| \leq n,
\end{equation}

\noindent which provides an upper bound on the norm of the attractors. This ensures the bees will eventually be more attracted to the hive than the other attractors combined. 

The memory indicator \(\gamma_i(x_k, t)\), given by Equation \(6\), determines whether a flower has been visited. This indicator function is active by default, becoming inactive only when the ``nectar value" \(T_i\) is negative. When an agent is near attractor \(i\), \(T_i\) decreases linearly. Equations \(7\) and \(8\) define the vectorized notation of the collective memory and attraction across all flowers, denoted as \(\gamma^T\) and \(G\) respectively. The overall first-order system for the bee's velocity is governed by the equation \(\dot{x_k} = V \frac{\gamma^T G + B}{\xi + ||\gamma^T G + B||}\), where \(V\) denotes the max speed which bees can fly and \(\xi\) is an acceleration factor that allows for non-uniform speed.

This comprehensive framework integrates behavioral and environmental factors to provide a nuanced understanding of bee movement. The equations consider not only the physical dynamics of flight, but also incorporate behavioral elements such as memory and preference, offering a detailed insight into the navigation patterns of bees.

%% Third Section
\section{Results}
With the model specifications above, we find that with sufficient parameter tuning, we obtain a highly effective dynamic model of bee movement over time. Given the parameters\\

\begin{center}
\begin{tabular}{c c c c c c}
\hline
Grid Size & $\alpha$ & $b$ & $T_i$ & $V$ & $\xi$ \\ % Variable names
100 & 0.005 & 0.01 & 3 & 500 & 0.02 \\ % Corresponding values
\hline
\end{tabular}
\end{center}

\noindent along with the random placement of flowers through the field, initial starting positions of bees along the bottom of the grid, and the beehive situated directly at the top, we obtain the plot shown in Figure \ref{fig:BeesEvolution}.

\begin{figure}[h]
\begin{center}
\includegraphics[height=2.5in]{bees_to_the_top.png}. 
\end{center}
\caption{Final model: Several bees visit several attractive flowers in sight on their way to their destination: the hive.}
\label{fig:BeesEvolution}
\end{figure}

As evidenced by the figure, bees starting at the bottom of the field move from flower to flower, seemingly distracted as they eventually make their way to their original goal of the beehive. Observing the simultaneous evolution of the bees' trajectories demonstrates the primary intent of our model.

Further exploration of the model can be done by exploring vector fields of the attraction force on a particular bee. Given that different preference vectors of bees and flowers alike will result in different vector fields, we randomly construct a single field with varying flower preferences. Our vector field proves particularly useful in uncovering the behavior of our bees after a specific flower has already been visited and highlights the discontinuous change in the bee's trajectory. These vector fields are shown in Figure \ref{fig:VectorField}.

This vector field also highlights how the attractors prevent our bee from progressing to its original goal. These distractions may form trapping regions, as seen by the whitespace in the vector fields. Once the attractors are visited, these trapping regions disappear, allowing the agent to progress towards its original goal once more. 
\begin{figure}[h]
\begin{center}
\includegraphics[height=2.5in]{vector_field_side_by_side.png}. 
\end{center}
\caption{Plots of the vector field for the sum attraction force acting on a bee at any given position in the field shown before the bee begins its trajectory (left) and after the bee has visited most of the flowers (right).}
\label{fig:VectorField}
\end{figure}

\begin{figure}[h]
\begin{center}
\includegraphics[height=2.5in]{alpha_tuning.png}. 
\end{center}
\caption{Realization of bee trajectories through a field for differing values of $\alpha$. When $\alpha$ is small, the bees have a wide range of vision and are easily distracted. When $\alpha$ is large, the bees experience narrow vision and consequently become laser-focused on the hive.}
\label{fig:AlphaTuning}
\end{figure}

Changing parameters affects the path the bees follow. In particular, we can tune the trajectory of the bees to visit more or less flowers by changing the parameters governing the Gaussian distance function. Figure \ref{fig:AlphaTuning} demonstrates how, by increasing the parameter $\alpha$ by 10-fold, we can narrow the radius of attraction, forcing the bees to ignore flowers further away.

%%Fourth Section
\section{Analysis/Conclusions}

The techniques employed in modeling the flight patterns of bumblebees navigating a field of attractors were well-suited to capture the essence of the chosen phenomenon. Our group's approach effectively simulated the nuanced dynamics of agent motion amidst multiple enticing destinations. However, with more time and resources, several avenues for refining the model could have been explored, potentially enhancing its accuracy and applicability.\\

\noindent \textbf{Appropriateness of Methods}\\

The selected methods proved appropriate for capturing the fundamental aspects of bee flight behavior. Leveraging differential equations to model the attraction between bees and flowers effectively represented the intricate interplay between agents and multiple attractors. The introduction of preference vectors and environmental factors provided a comprehensive understanding of bee navigation.\\

\noindent \textbf{Model Enhancements}\\

Given additional time, several improvements could have been implemented to further enhance the model's fidelity. A periodic beehive factor would allow an agent to start and end at the same destination, like a shopper entering and exiting a store. This factor, initially time-dependent, could evolve to account for daily activities or number of items visited, rendering the model more lifelike. \\

\noindent \textbf{Insights from the Model}\\

In its current form the model offers valuable insights into various real-world scenarios. As mentioned before, its adaptability extends beyond the study of bees to scenarios such as customer paths in stores, visitor trajectories in theme parks, or tourists navigating cities. Understanding customer preferences and the influential role of proximity, as modeled using the vision factor above, empowers retailers to optimize store layouts. By strategically incorporating secondary attractors while directing attention towards primary objectives, retailers can potentially enhance profits through well-crafted, distraction-driven layouts.\\

\noindent \textbf{Learning and Future Directions}\\

This project opened a window into the world of mathematical modeling, revealing its collaborative and creative essence. Working as a team to formulate meaningful equations without the structure of a standard homework assignment was eye opening. It allowed us to step into the shoes of mathematicians, crafting our unique path rather than echoing established concepts.

Future iterations of this model could explore the impact of differential preferences, diverse environmental factors, and complex interactions among agents, presenting opportunities for in-depth investigations into behavior-based modeling across diverse domains.

In conclusion, while the model effectively simulated the essential aspects of bee flight behavior, further refinements and extensions could amplify its realism and applicability across various real-world scenarios.\\









%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%% Bibliography below
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
\FloatBarrier % Keep the figures from being put after the bibliography
\newpage
%% If using bibtex, leave this uncommented
\bibliography{refs} %if using bibtex, call your bibtex file refs.bib
\bibliographystyle{alpha}

%% If not using bibtex, comment out the previous two lines and uncomment those below
%\begin{thebibliography}{99}
%\bibitem{Vandermeersch} First reference goes here
%\end{thebibliography}

\end{document}




% The model we created displays the motion of bees through a field, as seen in Figure \ref{fig:Complete Bee Model}. From our final model iteration, we conclude that bees are drawn towards flowers, primarily, those closest to a bees position, then secondarily to the more attractive ones. Some bees are encouraged to visit many flowers, while other bees only visit a few, likely a function of the preferences.
% % % \ref{fig:Without Gamma Factor}.
% % % \ref{fig:With Gamma Factor}.
% % \begin{figure}[h]
% \begin{center} %Put your images in a figure like this
% \includegraphics[height=2.5in]{traj_normal.png}. % Better to make them pdfs than png or gif or jpeg
% \end{center}
% \label{fig:Complete Bee Model}
% \caption{The trajectory of 5 bees in a field of 30 flowers, each flower with 5 features bees are attracted to, traveling for 1000 time steps.}
% \end{figure}


% \FloatBarrier

% \begin{figure}[h]
% \begin{minipage}[h]{0.47\linewidth}
% \begin{center}
% \includegraphics[width=\textwidth]{no_gamma.png}
% \end{center}
% \end{minipage}
% \begin{minipage}[h]{0.47\linewidth}
% \begin{center}
% \includegraphics[width=\textwidth]{gamma.png}
% \end{center}
% \end{minipage}
% \caption{Left: The path of bees without including the $\gamma$, notice the bees are stuck at flowers
% Right: The path of bees after including the $\gamma$ to prevent stopping at a flower}
% \label{fig:Gamma}
% \end{figure}


% Our model is robust at determining the position of an arbitrary amount of bees and and flowers within and defined field size. Furthermore, the model accurately shows the chaotic path bees travel towards flowers. Finally, our model successfully prevents the trajectory of bees from staying fixed at a particular flower, as demonstrated in Figure \ref{fig:Gamma}.

% Our model fails to account for the gradual depletion of attracting features, such as nectar in flowers. Furthermore, we do not consider how bees are either encouraged or discouraged from grouping together.
