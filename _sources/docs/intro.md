# Performance-Based Generative Design Workflows (PBGDW)

## What is Open Computational Design?
Computational design uses computational technologies to support and enhance the design process {cite}`yu_computational_2021,johnson_design_2016`. It requires designers to systematically assess the design process to apply the appropriate technologies in a timely and effective manner. Computational design offers new opportunities to design better built environments. It promises a seamless built-virtual-built cycle where designers are able to design virtually, translate the design to built artefact using digital fabrications, then again capture and represent the artefact virtually with sensors and reality capture technologies for refinement (illustrated in the image below). This cycle allows designers to refine their designs relatively cheaply and rapidly. **It is a powerful tool for the design of sustainable developments, where designers are able to meaningfully evaluate their designs against the Sustainable Development Goals (SDGs) throughout the design process**.
<br/><br/>
```{image} ../_static/digi_tech_full.gif
:width: 100%
:align: center
```
<br/><br/>
**An open computational design uses open standards in the process, and if possible Free and Open Source Software (FOSS) and Free and Open Source Hardware (FOSH)).** This will allow the process to be shared and readily extended to include new technologies as they emerge.

### Sustainable Development
Sustainable development is a direct reference to the <a href="https://sdgs.un.org/goals" target="_blank">17 United Nations Sustainable Development Goals (SDG)</a>.

## Why Open Computational Design?
Open computational design will significantly reduce the cost of technology adoption in practice and especially in places that cannot afford technologies. They are coincidentally located in the regions where rapid urbanization is taking place (e.g. Southeast Asia ...). I hope to encourage designers to share their design process openly for others to build upon. As new improvements are made, the industry and society stands to gain as we improve the way we design our built environment. This is only possible with the use of open-source technologies where everyone can contribute and modify the computational design process to suit their own needs.

Computational design process that uses proprietary technologies deters access for designers that cannot afford the technologies and prevent open sharing. For example, I have developed and openly shared an optimization routine in a proprietary CAD system called CADx. If DesignersY who uses CADy for their projects are interested in using the process. They need to buy CADx. Import the CADy file into CADx (provided the two CAD systems interoperate), learn a new software suite and run the routine. Modify and improve on the routine and contribute back to the source.

This is currently the dominant practice in computational design. Computational designers are converging to a few proprietary software packages, as a result many of the openly shared computational design process are not truly open. Middleware libraries (e.g. <a href="https://speckle.systems/" target="_blank">Speckle</a> and <a href="https://compas.dev/index.html" target="_blank">COMPAS</a>) are popular solutions to enable interoperability between these software packages. The middleware links the packages together to perform different tasks. The packages linked are primarily proprietary with a small mix of open-source packages. I can foresee as the task gets more complex, the process will require the use of more software. A few issues will arise with this current trend:

1. The middleware will have to accommodate ever more software. This is very high maintenance as one software package can easily break the whole process by accident through a routine software/API update. This is in comparison to using an agreed upon open standard for the exchange of information.
2. Designers will be required to purchase multiple software licenses just to perform a particular task. The cost will limit the adoption of the computational design in practice and prohibit it in developing regions.
3. The industry will be lock-in to a few technology vendors. The 'Network Effect' will increase the users of these technologies. To prevent lock-in, we need to keep the 'Switching Cost' low by using open standards for interoperability.

Open computational design is a response to the current situation. I recommend the use of open source technologies and open standards when sharing their design process. I hope to facilitate this through:

1. Improving computational literacy of designers so that they are capable of developing computational design process with open-source technologies and standards.
2. Development of open computational design technologies that facilitates the computational design process.
3. Develop and distribute open computational design process that supports the design of sustainable development.

This E-book is a learning resource for anyone who is interested in computational design. It provides the background knowledge and practical examples required to implement computational design in your own practice.  I have separated the book into three main parts. The **First Part: Background and Motivation** describes the motivation and context of my work. The **Second Part: Task** describes and shares example of open computational design process that supports sustainable developments. The **Third Part: Sub-Task** describes software-specific examples that can be used in supporting the design of sustainable developments.
