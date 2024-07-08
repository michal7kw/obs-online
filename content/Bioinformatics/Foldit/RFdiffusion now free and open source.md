---
title: RFdiffusion now free and open source
draft: false
tags:
  - ready
created: 2024-02-07 05:07
updated: 2024-07-07 14:42
modified: 2024-07-07T14:42:16+02:00
---

Related:
- [[Institute for Protein Design]]
- [[Paper - De novo design of protein structure and function with RFdiffusion]]

Today we are making [[RFdiffusion]], our artificial intelligence (AI) program that can *generate novel proteins* with potential applications in medicine, vaccines, and advanced materials, free for both non-profit and for-profit use under a governed license.

The software, which has been tested in our labs, is much faster and more capable than prior protein design tools.

Researchers can access RFdiffusion through the open-source online platform [ColabFold](https://colab.research.google.com/github/sokrypton/ColabDesign/blob/v1.1.1/rf/examples/diffusion.ipynb), which is part of the cloud-based Google Colaboratory. The code is also available for download from [GitHub](https://github.com/RosettaCommons/RFdiffusion).

[![](https://www.bakerlab.org/wp-content/uploads/2022/11/Oligomer_Density-e1680154520989-300x164.png)](https://www.bakerlab.org/2022/11/30/diffusion-model-for-protein-design/)

Read More: [Introducing RFd](https://www.ipd.uw.edu/2022/12/a-diffusion-model-for-protein-design/)[iffusion](https://www.bakerlab.org/2022/11/30/diffusion-model-for-protein-design/)

“With RFdiffusion, the power of AI can be harnessed to create useful proteins in a matter of seconds. We invite researchers from around the world to join us in this exciting journey of scientific discovery,” said Joe Watson, a lead developer of RFdiffusion and postdoctoral scholar in the [Baker Lab](https://www.bakerlab.org/).

RFdiffusion outperforms existing protein design methods across a broad range of problems, including:
- topology-constrained protein monomer design, 
- protein binder design, 
- symmetric oligomer design, 
- enzyme active site scaffolding,
- symmetric motif scaffolding for therapeutic and metal-binding protein design. 

RFdiffusion was developed by a team of computational biologists from UW Medicine, Columbia University, and MIT. Sergey Ovchinnikov, a John Harvard Distinguished Science Fellow at Harvard University, implemented RFdiffusion onto the ColabFold platform.

David Juergens, a lead developer of the software and doctoral student in the lab, said, “We’re thrilled to share RFdiffusion with the global research community, and we can’t wait to see the diverse and innovative ways scientists will apply this powerful AI tool.”

Drawing *inspiration from image generation tools like DALL-E*, the team developed RFdiffusion as a *[[guided diffusion model]]* for protein design. Proteins are composed of amino acid chains, and the sequence of these building blocks determines a protein’s structure and function, such as transporting oxygen in the blood, digesting food in the stomach, or sending signals in the brain.

To date, we have used RFdiffusion to create proteins that bind to target molecules, assemble into complex structures, and more. In one project, we created proteins that bind to hormones more tightly than any prior protein or small molecule designed on a computer. This breakthrough in creating proteins with enhanced binding properties has promising implications for the development of more effective diagnostics and therapies.

Proteins made via RFdiffusion have the potential to prevent infections, combat cancer, reverse autoimmune disorders, and serve as key components in advanced materials.

“We are making RFdiffusion freely available to empower researchers around the world. We believe it can help unlock new solutions to important challenges in medicine and beyond,” said Nate Bennett, a lead developer of RFdiffusion and doctoral student in the lab.

The development and initial testing of RFdiffusion is described in the journal [_Nature_](https://www.nature.com/articles/s41586-023-06415-8) and the preprint “[De novo design of high-affinity protein binders to bioactive helical peptides](https://www.biorxiv.org/content/10.1101/2022.12.10.519862v4).” Because of the considerable demand for the code, we are making it available to the public.
- [[Paper - De novo design of protein structure and function with RFdiffusion]]