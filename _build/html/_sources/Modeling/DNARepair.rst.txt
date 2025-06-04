Mechanistic Models of DNA Repair
=================================

The biological modeling within TOPAS-nBio has been developed by our external collaborators at Manchester University and Queens University Belfast (see :ref:`Collaboration`). Two models have been used together with TOPAS-nBio that are described in more detail in this section.


DaMaRiS
--------
DaMaRiS (DNA Mechanistic Repair Simulator) has been developed at the University of Manchester. DaMaRiS uses a distribution of DNA damage as input to model Non-Homologous End Joining (NHEJ) DNA repair kinetics. The input can be supplied via the SDD standard ([Schuemann2019]_). In addition, DaMaRiS has been integrated into the TOPAS-nBio framework, thus a single TOPAS-nBio simulation can include the entire process from initial cell irradiation to final outcome. The SDD-compatible DaMaRiS framework can be downloaded from https://gitlab.com/PRECISE-RT/releases/damaris_topas-nbio.

The methodology behind DaMaRiS has been published in [Warmenhoven2020]_, [Ingram2019]_, [Henthorn2019]_, [Henthorn2018]_, [Henthorn2017]_.


MEDRAS
--------

The MEDRAS (Mechanistic DNA Repair And Survival) model has been developed at Queen's University, Belfast. Beginning from an initial distribution of DNA damage, it probabilistically models the interaction of free DSB ends, based on a spatially-dependent interaction probability. This is used to calculate rates of both correct rejoining and misrepair. This process models the kinetics of repair through each of the three major repair processes (NHEM, Homologous Recombination (HR) and Backup/Microhomologoy Mediated End Joining (MMEJ) ), allowing the simulation of cells with different repair capacities. MEDRAS can generate predictions of a range of biological endpoints, including DSB repair kinetics as well as rates of double strand break misrepair, mutation, and chromosome aberrations. Survival can also be modelled for a range of different cell backgrounds.

The model has been described in [McMahon2016]_ and [McMahon2017]_. MEDRAS takes DSB damage distributions as input, but is (currently) an external Python-based program that can be linked to TOPAS-nBio via the SDD. The SDD-compatible MEDRAS model can be downloaded from https://github.com/sjmcmahon/Medras-MC.


References
----------

.. [Warmenhoven2020] Warmenhoven, J.W., Henthorn, N.T., Ingram, S.P., Chadwick, A.L., Sotiropoulos, M., Korabel, N., et al. (2020). Insights into the non-homologous end joining pathway and double strand break end mobility provided by mechanistic in silico modelling. DNA repair, 85, 102743. https://doi.org/10.1016/j.dnarep.2019.102743

.. [Ingram2019] Ingram, S. P., Warmenhoven, J. W., Henthorn, N. T., Smith, E. A. K., Chadwick, A. L., Burnet, N. G., et al. (2019). Mechanistic modelling supports entwined rather than exclusively competitive DNA double-strand break repair pathway. Scientific Reports, 9(1), 6359. http://doi.org/10.1038/s41598-019-42901-8

.. [Henthorn2019] Henthorn, N. T., Warmenhoven, J. W., Sotiropoulos, M., Aitkenhead, A. H., Smith, E. A. K., Ingram, S. P., et al. (2019). Clinically relevant nanodosimetric simulation of DNA damage complexity from photons and protons. RSC Advances, 9(12), 6845–6858. http://doi.org/10.1039/C8RA10168J

.. [Henthorn2018] Henthorn, N. T., Warmenhoven, J. W., Sotiropoulos, M., Mackay, R. I., Kirkby, N. F., Kirkby, K. J., & Merchant, M. J. (2018). In Silico Non-Homologous End Joining Following Ion Induced DNA Double Strand Breaks Predicts That Repair Fidelity Depends on Break Density. Scientific Reports, 8(1), 2654. http://doi.org/10.1038/s41598-018-21111-8

.. [Henthorn2017] Henthorn, N. T., Warmenhoven, J. W., Sotiropoulos, M., Mackay, R. I., Kirkby, K. J., & Merchant, M. J. (2017). Nanodosimetric Simulation of Direct Ion-Induced DNA Damage Using Different Chromatin Geometry Models. Radiation Research, 188(6), 770–783. http://doi.org/10.1667/RR14755.1

.. [McMahon2017] McMahon, S. J., McNamara, A. L., Schuemann, J., Paganetti, H., & Prise, K. M. (2017). A general mechanistic model enables predictions of the biological effectiveness of different qualities of radiation. Scientific Reports, 7(1), 688. http://doi.org/10.1038/s41598-017-10820-1

.. [McMahon2016] McMahon, S. J., Schuemann, J., Paganetti, H., & Prise, K. M. (2016). Mechanistic Modelling of DNA Repair and Cellular Survival Following Radiation-Induced DNA Damage. Scientific Reports, 6, 33290. http://doi.org/10.1038/srep33290

.. [Schuemann2019]	Schuemann J., McNamara A. L., Warmenhoven J., et al. (55 authors). (2019). A new Standard DNA Damage (SDD) data format. Radiation Research; 191(1):76. PMID: 30407901. PMCID: PMC6407706. https://doi.org/10.1667/RR15209.1
