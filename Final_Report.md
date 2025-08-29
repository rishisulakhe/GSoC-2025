<h1 align="center">Google Summer of Code 2025:  Work Product Submission</h1>

* **Name: [Rishi Prasad Sulakhe](https://github.com/rishisulakhe)**
* **Project: [Show Variant Allele Frequency on the Plots tab and Oncoprint](https://summerofcode.withgoogle.com/programs/2025/projects/8scNjJhi)**
* **Organization: [cBioPortal for Cancer Genomics](https://summerofcode.withgoogle.com/programs/2025/organizations/cbioportal-for-cancer-genomics)**
* **Mentor: [Onur Sumer](https://github.com/onursumer)**

This summer, I contributed to [cBioportal](https://www.cbioportal.org/) by integrating novel quantitative visualizations for Variant Allele Frequency (VAF) into its core cohort analysis tools. Under the guidance of my mentor [Onur Sumer](https://github.com/onursumer) I focused on bridging a key gap between the qualitative and quantitative analysis of genomic data. I implemented two major features: enabling the Plots Tab to generate box plots of VAF for distributional analysis across clinical groups, and introducing a new VAF heatmap track to the OncoPrint. These enhancements empower researchers to more effectively analyze tumor heterogeneity directly within the [Plots tab](https://www.cbioportal.org/study/plots?id=msk_impact_2017) and [Oncoprint](https://www.cbioportal.org/results/oncoprint?cancer_study_list=msk_impact_2017&Z_SCORE_THRESHOLD=2.0&RPPA_SCORE_THRESHOLD=2.0&profileFilter=mutations&case_set_id=msk_impact_2017_cnaseq&gene_list=TP53&geneset_list=%20&tab_index=tab_visualize&Action=Submit), ultimately providing a more comprehensive and intuitive data exploration experience.

* **Project Issue**: [Show Variant Allele Frequency on the Plots tab and Oncoprint](https://github.com/cBioPortal/GSoC/issues/122) 
* **Technologies**: JavaScript, TypeScript, React, MobX

<table>
  <tr>
    <td width="60%"><img src="https://i.ibb.co/gWGPvQF/Screenshot-2023-08-26-at-01-27-36.png"></td>
    <td width="40%"><img src="https://www.health-ri.nl/sites/healthri/files/styles/header_service/public/2020-03/cBioPortalLogo.png.JPG?itok=iSy_Gr9J"></td>
  </tr>
</table>

## 1. Background

The [cBioPortal](https://www.cbioportal.org/) for Cancer Genomics is an open-access, open-source resource for interactive exploration of multidimensional cancer genomics data sets. The goal of cBioPortal is to significantly lower the barriers between complex genomic data and cancer researchers by providing rapid, intuitive, and high-quality access to molecular profiles and clinical attributes from large-scale cancer genomics projects, and therefore to empower researchers to translate these rich data sets into biologic insights and clinical applications.

While cBioPortal excels at displaying the qualitative nature of mutations (e.g., missense, truncating, splice), a key quantitative attribute often remained difficult to visualize at a cohort level: the Variant Allele Frequency (VAF). VAF represents the percentage of sequenced DNA reads at a specific genomic position that contain a particular mutation. This metric is critical for understanding tumor biology, as it helps researchers distinguish between clonal mutations (high VAF, present in most tumor cells) and subclonal mutations (low VAF, present in a subset of cells), which is essential for studying tumor heterogeneity and evolution. 

## 2. Motivation and Project Goals

### Plots Tab 📊
- Researchers could not plot the distribution of VAF for mutation datatype across different clinical groups (e.g., Cancer Types). Analysis was   limited to categorical data (like Mutation Type), preventing quantitative comparisons of VAF

- **Goal:** Enable plotting VAF as a continuous variable, allowing box/scatter plots for direct quantitative comparisons across groups.

### OncoPrint 🧬

- The OncoPrint is one of cBioPortal's most iconic views, giving a brilliant summary of which genes are altered in which patients. You can see the type of alteration—a missense mutation, an amplification, and so on. Users could see mutation types but not their VAF, forcing them to check patient-level pages and losing the cohort context.

- **Goal:** Add a dedicated VAF heatmap track to the OncoPrint, enabling visualization of mutation type and prevalence side-by-side in one unified view.

Together, these enhancements bridge the gap between qualitative and quantitative mutation analysis, providing richer insights into tumor clonality within cBioPortal’s core analysis tools.ns.

## 3. Work Done

Before the GSoC coding period began, I familiarized myself with the cBioPortal codebase by contributing to various parts of the portal, resulting in several merged PRs. Once the official coding period started, I focused on the two primary goals outlined in my proposal. With guidance from my mentors, particularly Onur Sumer, I implemented the main features for adding VAF visualizations to the Plots tab and OncoPrint.

I had weekly meets with both my mentor where I showcased my progress. All my work was done on a forked repository from which pull requests were created. **Link**: [rishisulakhe/cBioportal-frontend](https://github.com/rishisulakhe/cBioportal-frontend)

### 3.1. Pull Requests Created during GSoC

1. **[Add Variant Allele Frequency on Plots tab ](https://github.com/cBioPortal/cbioportal-frontend/pull/5209)**

**Description:** This feature enhances the Plots Tab by allowing researchers to visualize Variant Allele Frequency (VAF) as a continuous numerical value. For the first time, users can now generate box plots to see the distribution of VAF for a specific gene across different categories, such as cancer types, enabling direct quantitative comparisons.

**Implementation Plan:**
My approach was to seamlessly integrate this new functionality into the existing plotting architecture.

- First, I introduced "Variant Allele Frequency" as a new option in the user interface dropdown for mutation data.

- When a user selects this option, the system's data processing logic is triggered. For each sample in the cohort, it retrieves the necessary read count data (`tumorAltCount` and `tumorRefCount`) for the selected gene's mutations.

- Using these values, it calculates the VAF for each mutation. The system is designed to handle cases where a single sample may have multiple mutations in the same gene.

- Finally, this numerical VAF data is packaged into a data structure that the main plotting component already understands. The component intelligently recognizes that it has received numerical data for one axis and categorical data (e.g., Cancer Types) for the other, and automatically renders the most appropriate visualization—a Box-Scatter Plot. This strategy of leveraging the existing rendering logic made the implementation clean and efficient.

<img width="1362" height="781" alt="image" src="https://gist.github.com/user-attachments/assets/5194653d-1696-4eba-aa6f-b6c03ffba3a0" />

**Challenges Faced:**
A significant challenge was handling studies that do not contain the necessary read count data required for VAF calculation. In these cases, my initial implementation would attempt to render a plot with empty or "NaN" (Not a Number) values, which was confusing for the user. To solve this, I implemented a pre-check that verifies if the selected study contains valid VAF data for the chosen gene. If no data is available, the system now gracefully displays a "No data available" message instead of an empty or broken chart, ensuring a much better user experience.

2. **[Add Variant Allele Frequency Heatmap in Oncoprint](https://github.com/cBioPortal/cbioportal-frontend/pull/5237)**

**Description:** 
This feature enriches the OncoPrint by introducing a new, user-configurable VAF heatmap track. It provides a critical quantitative layer to the OncoPrint, allowing researchers to see both a mutation's type (e.g., missense) and its VAF simultaneously in the main cohort view. This helps in quickly distinguishing between foundational, high-VAF mutations and minor, subclonal events.

**Implementation Plan:**

- First, I registered "Variant Allele Frequency" as a new type of molecular profile heatmap that the OncoPrint can display. This allowed it to appear as a selectable option in the "Heatmap" of "Track" menu alongside existing heatmaps like mRNA Expression and Methylation.

- When a user selects the Variant Allele Frequency option with specific genes, the system triggers a specialized data-handling process.The VAF track fetches the raw mutation data for that genes.

- For each mutation found in each sample, the VAF is calculated on-the-fly from the variant and reference read counts.

- This newly calculated VAF data is then formatted into the standard data structure that the OncoPrint's heatmap rendering engine already uses. This strategy of leveraging the existing, powerful rendering component made the implementation highly efficient.

<img width="1539" height="674" alt="image" src="https://gist.github.com/user-attachments/assets/12262960-a4ae-47d6-beb0-1ebd70d3ec54" />

**Challenges Faced:**
- A major technical challenge was ensuring the VAF heatmap's color scale rendered independently. Initially, the VAF track, which has a fixed 0-1 data range, would incorrectly inherit the color scale from other heatmaps like mRNA expression (e.g., -3 to +3). This rendered the VAF visualization meaningless.

- To solve this, I implemented a stricter rule-sharing mechanism. By assigning the VAF heatmap a unique molecular alteration type, I ensured that visualization rules, like color gradients, are only shared between tracks of the exact same type. This isolates the VAF heatmap's 0-to-1 scale, guaranteeing it always displays correctly.

My work not only involved creating new features but also included fixing bugs, writing tests, and ensuring that the new features were user-friendly. For example, I wrote tests for the Plots tab VAF data and Oncoprint heatmap generation to ensure their reliability.

### Contributions Prior to GSoC
| Pull Request  | Description | Status  |
| :-----------  | :----------:  | -------: |
| [#5125](https://github.com/cBioPortal/cbioportal-frontend/pull/5125)          | Added boxplot stats as tooltip for axis label  |  Merged  |
| [#5130](https://github.com/cBioPortal/cbioportal-frontend/pull/5130)          | Update legend with filtered categories in Plots tab  |  Merged  |
| [#5077](https://github.com/cBioPortal/cbioportal-frontend/pull/5077)          | fixes e2e tests to use CANCER_TYPE rather than tumor type  |  Merged  |
| [#5162](https://github.com/cBioPortal/cbioportal-frontend/pull/5162)          | Use of path URL for comparison and pathways subtab  |  Merged  |
| [#11500](https://github.com/cBioPortal/cbioportal/pull/11500)          | Added backend SPA forwarding support for result view comparison and pathways subtabs new routes  |  Merged  |
| [#5099](https://github.com/cBioPortal/cbioportal-frontend/pull/5099)          | Ordering some groups on comparison page  |  Open  |

## 4. Future goals

During the GSoC coding period, I successfully completed all the features outlined in my proposal, including VAF integration in both the Plots Tab and OncoPrint. However, there is still room for refinement and extension. I plan to continue contributing to cBioPortal beyond GSoC, actively maintaining and enhancing the features I developed and exploring new opportunities to add value to the project.

## 5. Acknowledgment

I would like to express my sincere gratitude to my mentor, [Onur Sumer](https://github.com/onursumer), for his invaluable support during the coding period. His advice on intregating VAF visualization along with his thorough code reviews and insightful comments, significantly improved the visual quality  and ensured that the code remained clean and maintainable.

Special Thanks to [Ino de Bruijn](https://github.com/inodb) and whole cBioportal team for providing immense support and help throughout the program.

I would also like to extend my thanks to [Google Summer of Code](https://summerofcode.withgoogle.com/) for providing me with this incredible opportunity to contribute to cBioPortal, a platform that has a meaningful impact on cancer genomics and healthcare.

Thanks and Regards,

Rishi Prasad Sulakhe


<table>
  <tr>
    <td width="50%"><img src="https://gist.github.com/user-attachments/assets/c413d087-e633-43c5-bbe1-bf47ade43e61"></td>
    <td width="50%"><img src="https://gist.github.com/user-attachments/assets/3e1c856f-2928-4b91-a504-ba6107886168"></td>
  </tr>
</table>


<p align=center>Google Summer of Code 2025</p>

