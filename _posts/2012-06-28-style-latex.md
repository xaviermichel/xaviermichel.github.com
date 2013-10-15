---
layout: post
category : projet
tagline: ""
tags : [latex, template, style]
---
{% include JB/setup %}

Voici un style latex que j'avais adapté afin de faire mes rapports dans ce merveilleux format. 

Voici [le résultat](/assets/posts/template_latex.pdf) que j'ai finalement obtenu.

*****

Voici [une archive avec le code source et des fichiers d'exemple](/assets/posts/template_latex.tar.gz).

Le code ci-dessous est le contenu du fichier "style.tex" (la définition même du style). Voir index.tex (dans l'archive) qui contient pas mal de trucs utiles pour démarrer rapidement un document.


    % Xavier MICHEL
     
    %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
     
    % packages
     
    \usepackage[utf8]{inputenc}
    \usepackage[francais]{babel}
    \usepackage[T1]{fontenc}
    \usepackage{color}
    \usepackage{xcolor}
    \usepackage{listings}
    \usepackage{fancyhdr}
    \usepackage{lastpage}
    \usepackage{graphicx}
    \usepackage{shorttoc}
    %\usepackage{picins}
    \usepackage{multirow}
    \usepackage{url}
    \usepackage{geometry}
    \usepackage{ifthen}
    \usepackage{times}
    \usepackage{psboxit}
    \usepackage[avantgarde]{quotchap}
    \usepackage[calcwidth]{titlesec}
    \usepackage[Glenn]{fncychap}	% ce package est génial, voir la doc : http://www.ctan.org/tex-archive/help/Catalogue/entries/fncychap.html
    %\usepackage[french]{varioref}	% ça a l'air marrant, voir faq latex developpez
     
    \ifpdf
    \usepackage[pdftex,
                pagebackref=true,
                colorlinks=false,
                linkcolor=black,
                unicode,
    		  pdfborder={0 0 0}
               ]{hyperref}
    \fi
     
     
    %% pour les tests
    %\usepackage{lipsum}
    %%
     
    %% et là il faut choisir l'une des font, en voilà une selection
    %% les mieux sont newcent et charter pour moi, newcent plus 'cool'
    %% mais charter plus formelle
    %\usepackage{lmodern}
    %\usepackage{mathpazo}
    \usepackage{newcent}
    %\usepackage{charter}
    %%
     
     
    %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
     
    % Couleurs
     
    % ex-titres
    %\definecolor{dockerblue}{rgb}{0.11,0.56,0.98}
    %\definecolor{freeblue}{rgb}{0.25,0.41,0.88}
    %\definecolor{myblue}{rgb}{0,0.2,0.4}
     
    % codes sources
    \definecolor{pink}  {rgb}{0.67, 0.05, 0.57} % keywords
    \definecolor{red}   {rgb}{0.87, 0.20, 0.00} % strings
    \definecolor{green} {rgb}{0.00, 0.47, 0.00} % comments
    \definecolor{violet}{rgb}{0.41, 0.12, 0.61} % classes
    \definecolor{blue}  {rgb}{0.21, 0.00, 0.44} % functions
    \definecolor{brown} {rgb}{0.39, 0.22, 0.13} % brow
     
     
    %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
     
    % styles des titres
     
    % partie is better than chapitre
    \addto\captionsfrench{\renewcommand{\chaptername}{Partie}}
     
    % Le chapitre doit s'imposer <img src="http://www.xaviermichel.info/data/smileys/tongue_laugh.gif" width="19" height="19" alt="tongue laugh" style="border:0;" />
    \ChTitleVar{\raggedleft\LARGE\sffamily\bfseries}
     
    % section
    \titleformat{\section}[hang]{\sffamily\bfseries}
    {\Large\thesection}{12pt}{\Large}[{\titlerule[0.5pt]}]
     
    % les subsections sont pas mal par défaut
     
    % subsubsection
    \titleformat{\subsubsection}[hang]{\sffamily\bfseries}
    {\thesection}{12pt}{\normalsize\underline}[{}]
    % ...
     
    %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
     
    % style des pages : marge et couleur de fond
     
    % Le package geometry permet de régler rapidement les marges !
    %\geometry{height=22cm,width=15cm}
    %\geometry{hmargin={3cm,3cm}}	% compensation pour les documents twoside, en cas de logo != sur chaque page
     
    %\pagecolor[rgb]{0.92,0.92,0.98} 	%couleur de fond de page
     
    \parskip 6pt %espace entre les paragraphes
    \renewcommand{\baselinestretch}{1.2} % interligne
    %\setlength{\parindent}{1cm} % espace tabulation début paragraphe
     
    %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
     
    % En tete et pieds de pages, piocher, commentez, décommentez et pleurez
     
    \makeatletter	% pour utiliser les variables tel que le titre : \@title
     
    %\fancyhf{}			%permet de vider les en-têtes et pieds de page par défaut de LaTeX afin de les personnaliser
    %\pagestyle{fancy}  	%définition du style d'en tête et de pied de page
     
    %\fancyhead[L]{\includegraphics[height=1cm]{images/DOS}}
    %\fancyhead[C]{\it My title}
    %\fancyhead[R]{\fontfamily{ppl}\selectfont\leftmark}
    %\fancyhead[R]{\includegraphics[height=1cm]{images/windows}}
    %\fancyfoot[L]{\leftmark}
    %\fancyfoot[R]{\fontfamily{ppl}\selectfont\thepage/\pageref{LastPage}}
     
    %\renewcommand{\footrulewidth}{2pt}		    %épaisseur de la ligne qui sépare le corps du texte et le pied de page
     
    %% Différents en-tête page pair/impaire /!\ mettre twoside en option de document
    %\fancyhead[LO]{\includegraphics[scale=0.2]{images/pythonR}}
    %\fancyhead[LE]{\includegraphics[scale=0.2]{images/pythonL}}
    %\fancyhead[RO]{\includegraphics[scale=0.2]{images/pythonL}}
    %\fancyhead[RE]{\includegraphics[scale=0.2]{images/pythonR}}
     
     
    %%% à partir de là c'est un autre style, totalement différement construit
     
    %% now reset the headers and footers
    \fancyhead{}
    \fancyfoot{}
     
    %\fancyhead[L]{\includegraphics[height=0.6cm]{images/logoPimentic.png}}
    \fancyhead[R]{\fontfamily{ppl}\selectfont\leftmark}
    %\fancyhead[R]{\includegraphics[height=0.6cm]{images/logoUTBM.png}}
     
    %% put the page numbers on the bottom in a nice box
    %% even side pages
    \fancyfoot[R]{\bfseries\thepage/\pageref{LastPage}}
    \fancyfoot[L]{\fontfamily{ptm}\selectfont Application android de partage convivial de photos}
     
    %% make the bottom line above the page number box
    \renewcommand{\footrulewidth}{0.4pt}
    \renewcommand{\footruleskip}{0mm}
     
    %% bring the style into effect
    \pagestyle{fancy}
     
    %% now redefine the plain style pages (chapter pages, contents pages)
    %% to have the same page number stuff on the bottom
    \fancypagestyle{plain}{
    	\fancyhf{}
    	\fancyfoot[R]{\bfseries\thepage/\pageref{LastPage}}
    	\renewcommand{\headrulewidth}{0pt}
    	\renewcommand{\footrulewidth}{0.5pt}
    	\fancyfoot[L]{\fontfamily{ptm}\selectfont Application android de partage convivial de photos}
    }
     
    \makeatother
     
     
    %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
     
    % style des listes à puce
     
    \AtBeginDocument{
    	\renewcommand{\labelitemi}{$\bullet$}
    	\renewcommand{\labelitemii}{$\bullet$}
    	\renewcommand{\labelitemiii}{$\bullet$}
    }
     
    %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
     
    % style de mise en page des codes sources
     
    \lstset{
    	basicstyle=\small,
    	identifierstyle = \color{black},
    	commentstyle    = \color{green},
    	keywordstyle    = \color{pink},
    	stringstyle     = \color{red},
    	%directivestyle  = color{brown},
    	showstringspaces=false,
    	frameround=ffff,	% bords arrondis, t=arrondie, f=normal
    	frame=l,			% bordures tblr ou TBLR
    	framerule=0.05cm,
    	%backgroundcolor=une couleur,
    	breaklines=true,
    	breakatwhitespace=true,
    	breakautoindent = true,
    	tabsize=4,
    	extendedchars=true,
    	inputencoding=latin1,
    	numbers=left,
    	stepnumber=5,
    	numberstyle=\tiny,
    	xleftmargin=1cm,
    	numbersep=10pt
    }
     
     
    \lstdefinestyle{terminal}{
    	backgroundcolor=\color{black},
    	frame=,
    	frameround=ffff,
    	tabsize=2,
    	basicstyle=\small \color{white},
    	identifierstyle = \color{white},
    	morekeywords={echo},
    	keywordstyle=\color{red}
    }
     
    %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
     
    % refonte du style de la page de garde
    \makeatletter
     
    \def\thickhrulefill{\leavevmode \leaders \hrule height 1pt\hfill \kern \z@}
    \renewcommand{\maketitle}{\begin{titlepage}%
        \let\footnotesize\small
        \let\footnoterule\relax
        \parindent \z@
        \reset@font
     
        \vspace*{3cm}
     
        \hrule height 1pt
     
        %%% titre & date
    	\begin{flushright}
    		\Large
    		\@title
          %\vskip 30\p@
    	\end{flushright}
     
        \hrule height 1pt
     
    	\vfill
     
    	%%% entreprise
    	\begin{center}
    		\begin{figure}[h]
    			\center
    			\includegraphics[width=8cm]{images/utbm}
    		\end{figure}
    	\end{center}
     
    	\vfill
     
    	%%% auteur
    	\parbox{.6\textwidth}{
    		\Large\@author
    	}
     
    	%%% date
    	\begin{flushright}
    		\large\it\@date
    	\end{flushright}
     
     
    	\end{titlepage}%
    	\setcounter{footnote}{0}%
    }
    \makeatother
     
     
    %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
     
    %%% données attachées au pdf
     
    \hypersetup{
    	backref=true, %permet d'ajouter des liens dans...
    	pagebackref=true,%...les bibliographies
    	hyperindex=true, %ajoute des liens dans les index.
    	colorlinks=false, %colorise les liens
    	breaklinks=true, %permet le retour à la ligne dans les liens trop longs
    	urlcolor= false, %couleur des hyperliens
    	linkcolor= black, %couleur des liens internes
    	bookmarks=true, %créé des signets pour Acrobat
    	bookmarksopen=true, %si les signets Acrobat sont créés,
    	%les afficher complètement.
    	pdftitle={}, %informations apparaissant dans
    	pdfauthor={Xavier MICHEL}, %dans les informations du document
    	pdfsubject={} %sous Acrobat.
    }
     
    %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
     
    %%% macros & environnements
     
    \newcommand{\TODO}[1]{{\large\bf\it\color{red}TODO~:~#1}}
    \newcommand{\mr}{M\up{r}}
    \newcommand{\mme}{M\up{me}}

