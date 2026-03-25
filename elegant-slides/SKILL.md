---
name: elegant-slides
description: Apply the Elegant DS beamer theme to a new or existing slide deck. Creates the styles/ folder with all theme files, loadslides.tex, and a starter slides.tex. Use when the user wants to create slides with the elegant DS theme, or apply the navy/coral/slate academic style.
allowed-tools: Bash(pdflatex*), Bash(latexmk*), Bash(biber*), Bash(ls*), Bash(mkdir*), Read, Write, Edit, Glob, Grep
argument-hint: [project-folder-or-topic]
---

# Elegant DS Beamer Theme

Apply the user's custom Elegant DS beamer theme to create professional academic presentations.

## What this theme provides

- **5 color palettes**: ds (navy/coral/slate, default), gold, orange, red, gray
- **Clean section dividers**: white background, big centered black title
- **Elegant footer**: author / title / frame count in small caps
- **Small-caps frame titles** with primary color
- **Table of contents** with dot leaders and page numbers
- **Code environments**: `rcode`, `shellbox`, `codebox`
- **Utility macros**: `\cmd{}`, `\file{}`, `\pkg{}`, `\robj{}`, `\dsnote{}`, `\dstip{}`, `\promptbox{}`, `\hlc{}`, `\hlb{}`, `\step{}`, `\twocol{}{}`
- **Block styles**: standard, alerted, example with theme colors

## Step 1: Create the styles/ folder

Create `styles/` in the project directory and write all 7 theme files from the templates below.

## Step 2: Create loadslides.tex

Write `loadslides.tex` in the project root from the template below. Adjust `\addbibresource{}` to match the user's .bib file (or remove if no bibliography).

## Step 3: Create slides.tex (if new deck)

Write a starter `slides.tex` using the skeleton template below.

## Step 4: Compile

Run `pdflatex -interaction=nonstopmode slides.tex` (run twice for TOC, plus `biber slides` if bibliography is used).

---

## FILE TEMPLATES

Write each file EXACTLY as shown below. Do not modify the theme files.

### styles/elegantmacros.sty

```latex
% Copyright (c) 2022 by Lars Spreng
% Creative Commons Attribution 4.0 International License.

\mode<presentation>

\mode<presentation>

\def\beamer@calltheme#1#2#3{%
    \def\beamer@themelist{#2}
    \@for\beamer@themename:=\beamer@themelist\do
    {\usepackage[{#1}]{\beamer@themelocation/#3\beamer@themename}}}

\def\usefolder#1{
    \def\beamer@themelocation{#1}
  }

\mode<all>
```

### styles/beamerthemeelegant.sty

```latex
\mode<presentation>
\DeclareOptionBeamer{style}{
    \PassOptionsToPackage{style=#1}{beamercolorthemeelegant}
}
\ExecuteOptionsBeamer{style=ds}
\ProcessOptionsBeamer
\usecolortheme{elegant}
\usefonttheme{elegant}
\useinnertheme{elegant}
\useoutertheme{elegant}
\setbeamertemplate{navigation symbols}{}
% 20cm x 13cm paper, 16:10-ish
\makeatletter
    \setlength\beamer@paperwidth{20.00cm}%
    \setlength\beamer@paperheight{13.00cm}%
    \geometry{papersize={\beamer@paperwidth,\beamer@paperheight}}
\makeatother
\setbeamersize{text margin left=12mm, text margin right=12mm}

\mode<all>
```

### styles/beamercolorthemeelegant.sty

```latex
\mode<presentation>

\RequirePackage{xcolor}

\definecolor{Gray}{HTML}{808080}

\DeclareOptionBeamer{style}{\def\beamer@mytheme@style{#1}}
\ExecuteOptionsBeamer{style=ds}
\ProcessOptionsBeamer

\def\beamer@mytheme@styleds{ds}%
\def\beamer@mytheme@stylegold{gold}%
\def\beamer@mytheme@styleorange{orange}%
\def\beamer@mytheme@stylered{red}%
\def\beamer@mytheme@stylegray{gray}%

\ifx\beamer@mytheme@style\beamer@mytheme@styleds%
    \definecolor{primary}{HTML}{1B2A4A}
    \definecolor{secondary}{HTML}{E8735A}
    \definecolor{tertiary}{HTML}{5C7A99}
    \definecolor{text}{HTML}{2C3E50}
    \setbeamercolor*{framesubtitle}{fg=tertiary,bg=white}
\else\ifx\beamer@mytheme@style\beamer@mytheme@stylegold%
    \definecolor{primary}{HTML}{08457E}
    \definecolor{secondary}{HTML}{B8860B}
    \definecolor{tertiary}{HTML}{B22222}
    \definecolor{text}{HTML}{000000}
    \setbeamercolor*{framesubtitle}{fg=secondary,bg=white}
\else\ifx\beamer@mytheme@style\beamer@mytheme@stylered%
    \definecolor{primary}{HTML}{08457E}
    \definecolor{secondary}{HTML}{B02A30}
    \definecolor{tertiary}{HTML}{006400}
    \definecolor{text}{HTML}{000000}
    \setbeamercolor*{framesubtitle}{fg=secondary,bg=white}
\else\ifx\beamer@mytheme@style\beamer@mytheme@styleorange%
    \definecolor{primary}{HTML}{08457E}
    \definecolor{secondary}{HTML}{CC5500}
    \definecolor{tertiary}{HTML}{008B8B}
    \definecolor{text}{HTML}{000000}
    \setbeamercolor*{framesubtitle}{fg=secondary,bg=white}
\else\ifx\beamer@mytheme@style\beamer@mytheme@stylegray%
    \definecolor{primary}{HTML}{08457E}
    \definecolor{secondary}{HTML}{E60073}
    \definecolor{tertiary}{HTML}{33CCCC}
    \definecolor{text}{HTML}{424242}
    \setbeamercolor*{framesubtitle}{fg=Gray,bg=white}
\else%
    \definecolor{primary}{HTML}{08457E}
    \definecolor{secondary}{HTML}{F209B1}
    \definecolor{tertiary}{HTML}{33CCCC}
    \definecolor{text}{HTML}{000000}
    \setbeamercolor*{framesubtitle}{fg=secondary,bg=white}
\fi\fi\fi\fi\fi%

% DS color aliases
\colorlet{dsPrimary}{primary}
\colorlet{dsAccent}{secondary}
\colorlet{dsSlate}{tertiary}
\definecolor{dsLight}{HTML}{F5F7FA}
\definecolor{dsWhite}{HTML}{FFFFFF}
\colorlet{dsText}{text}
\definecolor{dsMid}{HTML}{DCE4EE}

% Beamer color assignments
\setbeamercolor*{background canvas}{bg=white}
\setbeamercolor*{footer}{fg=Gray}
\setbeamercolor*{footer text}{fg=primary}
\setbeamercolor*{footline}{fg=Gray}
\setbeamercolor*{frametitle}{fg=primary,bg=white}
\setbeamercolor*{title}{fg=primary,bg=white}
\setbeamercolor*{author}{fg=text,bg=white}
\setbeamercolor*{institute}{fg=tertiary,bg=white}
\setbeamercolor*{date}{fg=tertiary,bg=white}
\setbeamercolor*{part title}{fg=primary}
\setbeamercolor*{caption name}{fg=text}
\setbeamercolor*{section in toc}{fg=black}

\setbeamercolor*{alerted text}{fg=secondary,bg=white}
\setbeamercolor*{example text}{fg=tertiary,bg=white}
\setbeamercolor*{normal text}{fg=text,bg=white}

\setbeamercolor*{itemize item}{fg=primary}
\setbeamercolor*{itemize subitem}{fg=secondary}
\setbeamercolor*{itemize subsubitem}{fg=tertiary}
\setbeamercolor*{enumerate item}{fg=primary}
\setbeamercolor*{enumerate subitem}{fg=secondary}
\setbeamercolor*{description item}{fg=primary}

\setbeamercolor{button}{bg=primary,fg=white}

\setbeamercolor{block title}{bg=white, fg=primary}
\setbeamercolor{block body}{fg=text}
\setbeamercolor{block title alerted}{bg=white, fg=secondary}
\setbeamercolor{block body alerted}{bg=white, fg=text}
\setbeamercolor{block title example}{bg=white, fg=tertiary}
\setbeamercolor{block body example}{bg=white, fg=text}

\setbeamercolor{section page}{fg=white, bg=primary}

\setbeamercolor*{bibliography item}{fg=primary}
\setbeamercolor*{bibliography entry author}{fg=primary}
\setbeamercolor*{bibliography entry title}{fg=text}
\setbeamercolor*{bibliography entry location}{fg=text}
\setbeamercolor*{bibliography entry note}{fg=text}

\mode<all>
```

### styles/beamerfontthemeelegant.sty

```latex
\mode<presentation>

\setbeamerfont{title}{series=\scshape,parent=structure}
\setbeamerfont{subtitle}{series=\scshape,parent=structure}

\setbeamerfont{frametitle}{series=\scshape,parent=structure}

\setbeamerfont{framesubtitle}{series=\normalfont}
\setbeamerfont{author}{series=\bfseries,parent=structure}
\setbeamerfont*{footnote}{size=\fontsize{6}{6}}
\setbeamerfont*{caption name}{series=\bfseries}

\setbeamerfont*{toc title}{size=\normalfont,series=\bfseries}
\setbeamerfont*{toc subtitle}{size=\small}
\setbeamerfont{block title}{series=\bfseries}%

\setbeamerfont*{bibliography entry author}{size=\normalsize,series=\normalfont}
\setbeamerfont*{bibliography entry title}{size=\normalsize,series=\bfseries}
\setbeamerfont*{bibliography entry location}{size=\small,series=\normalfont}
\setbeamerfont*{bibliography entry note}{size=\small,series=\normalfont}

\mode<all>
```

### styles/beamerouterthemeelegant.sty

```latex
\mode<presentation>

% Frame title
\setbeamertemplate{frametitle}{%
  \vspace{0.9cm}
  \usebeamercolor*[fg]{frametitle}\usebeamerfont{frametitle}\insertframetitle
  \vspace*{0.4cm}%
}

% Auto frame title from section when empty
\makeatletter
  \CheckCommand*\beamer@checkframetitle{%
    \@ifnextchar\bgroup\beamer@inlineframetitle{}}
  \renewcommand*\beamer@checkframetitle{%
    \global\let\beamer@frametitle\relax\@ifnextchar%
    \bgroup\beamer@inlineframetitle{}}
\makeatother

\addtobeamertemplate{frametitle}{%
  \ifx\insertframetitle\empty
    \frametitle{\insertsectionhead}%
  \fi
}{}

% Footer
\setbeamertemplate{footline}{%
  \hspace*{10mm}%
  {\usebeamerfont{footline}\scshape%
   \color{Gray}\insertshortauthor
   \hfill
   \color{primary}\insertshorttitle
   \hfill
   \color{Gray}\insertframenumber\,/\,\inserttotalframenumber%
  }%
  \hspace*{10mm}%
  \vspace*{5mm}%
}

\mode<all>
```

### styles/beamerinnerthemeelegant.sty

```latex
\mode<presentation>

\RequirePackage{tikz}
\usetikzlibrary{calc}
\RequirePackage{listings}
\RequirePackage{etoolbox}

% Table of Contents with dot leaders + page numbers
\makeatletter
\AtBeginPart{%
  \beamer@tocsectionnumber=0\relax
  \setcounter{section}{0}
}
\makeatother

\newcounter{sectionpage}

\makeatletter
\newcommand \Dotfill {\leavevmode \cleaders \hb@xt@ 0.8em{\hss .\hss }\hfill \kern \z@}
\makeatother

\makeatletter
\patchcmd{\beamer@section}
    {\addtocontents{toc}{\protect\beamer@sectionintoc{\the\c@section}{#2}{\the\c@page}{\the\c@part}{\the\beamer@tocsectionnumber}}}
    {\setcounter{sectionpage}{\insertframenumber}\stepcounter{sectionpage}%
     \addtocontents{toc}{\protect\beamer@sectionintoc{\the\c@section}{#2\Dotfill\thesectionpage}{\the\c@page}{\the\c@part}{\the\beamer@tocsectionnumber}}}
    {}{}
\makeatother

\setbeamertemplate{section in toc}{%
  \leavevmode{\bfseries\color{black}\inserttocsectionnumber}\hspace{0.3cm}%
  \textbf{\color{black}\inserttocsection}\vspace{0.3em}%
}
\setbeamertemplate{subsection in toc}{%
  \leavevmode\leftskip=4.2em\rlap{\hskip-2em\small\inserttocsectionnumber.\inserttocsubsectionnumber}%
  \hspace{0.2cm}\small\inserttocsubsection\par\vspace{0.3em}%
}

% Title page
\setbeamertemplate{title page}{%
  \vspace{2.5em}
  {\usebeamerfont{title}\color{primary}\inserttitle}\par
  \vspace{0.4em}
  {\color{secondary}\rule{4cm}{0.6pt}}\par
  \vspace{0.4em}
  {\usebeamerfont{subtitle}\color{tertiary}\insertsubtitle}\par
  \vspace{1.2em}
  {\usebeamerfont{author}\color{text}\insertauthor}\par
  \vspace{0.15em}
  {\usebeamerfont{institute}\color{tertiary}\insertinstitute}\par
  \vspace{0.15em}
  {\usebeamerfont{date}\color{tertiary}\insertdate}
}

% Section divider slides — white background, big centered title
\AtBeginSection[]{%
  {%
  \setbeamercolor{background canvas}{bg=white}%
  \begin{frame}[plain,c]
    \centering
    {\color{black}\Huge\bfseries\insertsection}
  \end{frame}
  }%
}

% Blocks
\setbeamertemplate{block begin}{%
  \par\vskip\medskipamount%
  \begin{beamercolorbox}[colsep*=2mm]{block title}
    \usebeamerfont*{block title}\insertblocktitle%
  \end{beamercolorbox}%
  {\parskip0pt\par}%
  \ifbeamercolorempty[bg]{block title}{}
  {\ifbeamercolorempty[bg]{block body}{}{\nointerlineskip\vskip-0.5pt}}%
  \usebeamerfont{block body}%
  \begin{beamercolorbox}[colsep*=2mm,vmode]{block body}%
    \ifbeamercolorempty[bg]{block body}{\vskip-.25ex}{\vskip-.75ex}\vbox{}%
}
\setbeamertemplate{block alerted begin}{%
  \par\vskip\medskipamount%
  \begin{beamercolorbox}[colsep*=2mm]{block title alerted}
    \usebeamerfont*{block title alerted}\insertblocktitle%
  \end{beamercolorbox}%
  {\parskip0pt\par}%
  \ifbeamercolorempty[bg]{block title alerted}{}
  {\ifbeamercolorempty[bg]{block body alerted}{}{\nointerlineskip\vskip-0.5pt}}%
  \usebeamerfont{block body alerted}%
  \begin{beamercolorbox}[colsep*=2mm,vmode]{block body alerted}%
    \ifbeamercolorempty[bg]{block body alerted}{\vskip-.25ex}{\vskip-.75ex}\vbox{}%
}
\setbeamertemplate{block example begin}{%
  \par\vskip\medskipamount%
  \begin{beamercolorbox}[colsep*=2mm]{block title example}
    \usebeamerfont*{block title example}\insertblocktitle%
  \end{beamercolorbox}%
  {\parskip0pt\par}%
  \ifbeamercolorempty[bg]{block title example}{}
  {\ifbeamercolorempty[bg]{block body example}{}{\nointerlineskip\vskip-0.5pt}}%
  \usebeamerfont{block body example}%
  \begin{beamercolorbox}[colsep*=2mm,vmode]{block body example}%
    \ifbeamercolorempty[bg]{block body example}{\vskip-.25ex}{\vskip-.75ex}\vbox{}%
}

% Lists
\setbeamertemplate{itemize item}{\textbullet}
\setbeamertemplate{itemize subitem}[circle]
\setbeamerfont{itemize/enumerate subbody}{size=\normalfont}

% Captions
\setbeamertemplate{caption label separator}[period]

% Text macros
\newenvironment{emphasize}[1]{\textcolor{primary}{#1}}{}
\providecommand{\eexample}[1]{\textcolor{tertiary}{#1}}

% Code environments
\lstset{
  basicstyle    = \ttfamily\scriptsize,
  keywordstyle  = \color{primary}\bfseries,
  commentstyle  = \color{tertiary}\itshape,
  stringstyle   = \color{secondary},
  showstringspaces = false,
  breaklines    = true,
  breakatwhitespace = true,
  columns       = flexible,
  keepspaces    = true,
}

\lstnewenvironment{rcode}[1][]{%
  \lstset{language=R, backgroundcolor=\color{dsLight},
    frame=single, framerule=0pt, rulecolor=\color{dsLight},
    xleftmargin=4pt, xrightmargin=2pt, #1}%
}{}

\lstnewenvironment{shellbox}[1][]{%
  \lstset{language={}, backgroundcolor=\color{primary},
    basicstyle=\ttfamily\scriptsize\color{white},
    keywordstyle=\color{secondary},
    frame=single, framerule=0pt, rulecolor=\color{primary},
    xleftmargin=4pt, xrightmargin=2pt, #1}%
}{}

\lstnewenvironment{codebox}[2][]{%
  \lstset{language=#2, backgroundcolor=\color{dsLight},
    frame=single, framerule=0pt, rulecolor=\color{dsLight},
    xleftmargin=4pt, #1}%
}{}

\mode<all>
```

### styles/dsmacros.sty

```latex
%% dsmacros.sty — utility macros
\ProvidesPackage{dsmacros}[2024/01/01 DS Macros]
\RequirePackage{xcolor}
\RequirePackage{mdframed}

% Inline code
\newcommand{\cmd}[1]{{\ttfamily\small\color{secondary}#1}}
\newcommand{\file}[1]{{\ttfamily\small\color{tertiary}#1}}
\newcommand{\pkg}[1]{{\bfseries\color{tertiary}#1}}
\newcommand{\robj}[1]{{\ttfamily\color{primary}#1}}

\newcommand{\key}[1]{%
  \begingroup\setlength{\fboxsep}{1.5pt}%
  {\footnotesize\fbox{\ttfamily\color{primary}#1}}%
  \endgroup
}

% Callout boxes
\newcommand{\dsnote}[1]{%
  \begin{beamercolorbox}[wd=\linewidth, sep=0.4em, rounded=true]{block body alerted}
    {\small\color{secondary}\textbf{Note:}~}{\small\color{text}#1}
  \end{beamercolorbox}%
}

\newcommand{\dstip}[1]{%
  \begin{beamercolorbox}[wd=\linewidth, sep=0.4em, rounded=true]{block body example}
    {\small\color{tertiary}\textbf{Tip:}~}{\small\color{text}#1}
  \end{beamercolorbox}%
}

% Prompt box
\newcommand{\promptbox}[1]{%
  \colorbox{primary}{\parbox{\dimexpr\linewidth-2\fboxsep\relax}{%
    \vspace{2pt}{\ttfamily\scriptsize\color{white}#1}\vspace{2pt}}}%
}

% Two-column shorthand
\newcommand{\twocol}[3][0.48]{%
  \begin{columns}[T]
    \begin{column}{#1\textwidth}#2\end{column}
    \hfill
    \begin{column}{#1\textwidth}#3\end{column}
  \end{columns}%
}

% Highlighting
\newcommand{\hlc}[1]{{\color{secondary}\bfseries #1}}
\newcommand{\hlb}[1]{{\color{primary}\bfseries #1}}

% Step counter
\newcounter{dsstep}
\newcommand{\resetsteps}{\setcounter{dsstep}{0}}
\newcommand{\step}[1]{%
  \stepcounter{dsstep}%
  \medskip\noindent
  {\color{secondary}\bfseries\large\thedsstep.}~~{\color{text}#1}\par
}
```

### loadslides.tex

```latex
% loadslides.tex — Package loading & theme configuration

% Theme
\usepackage{styles/elegantmacros}
\usefolder{styles}
\usetheme[style=ds]{elegant}

% Encoding & fonts
\usepackage[T1]{fontenc}
\usepackage[utf8]{inputenc}
\usepackage{lmodern}
\usepackage{microtype}

% Math
\usepackage{amsmath, amssymb, amsfonts}

% Graphics
\usepackage{graphicx}
\usepackage{tikz}
\usetikzlibrary{calc, positioning, arrows.meta, shapes.geometric}
\usepackage{pgfplots}
\pgfplotsset{compat=1.18}

% Tables
\usepackage{booktabs}
\usepackage{tabularx}
\usepackage{multirow}
\usepackage{array}
\usepackage{tabularray}
\usepackage{float}
\usepackage[normalem]{ulem}
\UseTblrLibrary{booktabs}
\UseTblrLibrary{siunitx}
\newcommand{\tinytableTabularrayUnderline}[1]{\underline{#1}}
\newcommand{\tinytableTabularrayStrikeout}[1]{\sout{#1}}
\NewTableCommand{\tinytableDefineColor}[3]{\definecolor{#1}{#2}{#3}}

% Numbers
\usepackage{siunitx}
\sisetup{group-separator={,}, group-minimum-digits=4}

% Colors
\usepackage{xcolor}

% Code listings
\usepackage{listings}
\AtBeginDocument{%
  \lstset{
    basicstyle    = \ttfamily\scriptsize,
    keywordstyle  = \color{primary}\bfseries,
    commentstyle  = \color{tertiary}\itshape,
    stringstyle   = \color{secondary},
    showstringspaces = false,
    breaklines    = true,
    breakatwhitespace = true,
    columns       = flexible,
    keepspaces    = true,
    frame         = none,
    xleftmargin   = 0pt,
  }%
}

% Tcolorbox
\usepackage{tcolorbox}
\tcbuselibrary{skins, listings}

% Hyperlinks
\hypersetup{
  colorlinks  = true,
  linkcolor   = primary,
  urlcolor    = tertiary,
  citecolor   = tertiary,
}

% Bibliography (adjust \addbibresource to your .bib file)
\usepackage[backend=biber,
            style=authoryear-comp,
            maxcitenames=2,
            maxbibnames=6,
            url=false,
            doi=false]{biblatex}
% \addbibresource{references.bib}

% Layout helpers
\usepackage{multicol}
\usepackage{soul}

% Custom macros
\usepackage{styles/dsmacros}

% Suppress warnings
\pdfsuppresswarningpagegroup=1
```

### Starter slides.tex skeleton

When creating a new deck, use this skeleton:

```latex
\documentclass[aspectratio=1610, 10pt]{beamer}

\graphicspath{{figures/}}
\input{loadslides}

\title[Short Title]{Full Presentation Title}
\subtitle{Subtitle Here}
\author{Author Name}
\institute{Institution}
\date{Date}

\begin{document}

{
\setbeamertemplate{footline}{}
\begin{frame}[plain]
  \titlepage
\end{frame}
}

\begin{frame}{Outline}
  \tableofcontents[hideallsubsections]
\end{frame}

\section{First Section}

\begin{frame}{First Slide Title}
  Content here.
\end{frame}

\end{document}
```

## Color palette options

Pass via `\usetheme[style=X]{elegant}` in loadslides.tex:

| Style    | Primary       | Secondary     | Tertiary      |
|----------|--------------|--------------|--------------|
| `ds`     | Navy #1B2A4A | Coral #E8735A | Slate #5C7A99 |
| `gold`   | Blue #08457E | Gold #B8860B  | Red #B22222   |
| `red`    | Blue #08457E | Red #B02A30   | Green #006400 |
| `orange` | Blue #08457E | Orange #CC5500| Teal #008B8B  |
| `gray`   | Blue #08457E | Pink #E60073  | Cyan #33CCCC  |
