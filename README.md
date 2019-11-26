Notes on modernizing CMS particle flow, in particular [PFBlockAlgo](https://github.com/cms-sw/cmssw/blob/master/RecoParticleFlow/PFProducer/src/PFBlockAlgo.cc) and [PFAlgo](https://github.com/cms-sw/cmssw/blob/master/RecoParticleFlow/PFProducer/src/PFAlgo.cc).

## Presentations

- CMS PF group, 2019-11-08: https://indico.cern.ch/event/861409/contributions/3632204/attachments/1941376/3219105/2019_11_08.pdf
- Caltech ML meeting, 2019-10-31: https://indico.cern.ch/event/858644/contributions/3623446/attachments/1936711/3209684/2019_10_07_pf.pdf
- Caltech ML meeting, 2019-09-19: https://indico.cern.ch/event/849944/contributions/3572113/attachments/1911520/3158764/2019_09_18_pf_ml.pdf
- CMS PF group, 2019-09-10: https://indico.cern.ch/event/846887/contributions/3557300/attachments/1904664/3145310/2019_09_10_pf_refactoring.pdf
- Caltech ML meeting, 2019-09-05: https://indico.cern.ch/event/845349/contributions/3554787/attachments/1902837/3141723/2019_09_05_pfalgo.pdf

## Other relevant issues, repos, PR-s:

- https://github.com/jpata/cmssw/issues/56

## Setting up the code
```bash
source /cvmfs/cms.cern.ch/cmsset_default.sh
export SCRAM_ARCH=slc7_amd64_gcc820
scramv1 project CMSSW CMSSW_11_0_0_pre12
cd CMSSW_11_0_0_pre12/src
eval `scramv1 runtime -sh`
git cms-init
mkdir workspace
git clone https://github.com/jpata/particleflow.git workspace/particleflow 
```


## Running the RECO step with particle flow
```
runTheMatrix.py -l 38.0
edmConfigDump step3.py > step3_dump.py
```

## Small standalone example
```bash
cmsRun test/step3.py

python3 test/ntuplizer.py ./data step3_AOD.root
root -l ./data/step3_AOD.root

python3 test/graph.py ./data/step3_AOD.root
ls ./data/step3_AOD_*.npz

```

## Running on grid
```bash
#Run the crab jobs
cd test
python multicrab.py
cd ..

#Make the ROOT ntuple (edit Makefile first)
make ntuples

#make the numpy cache
make cache
```

## Datasets

- October 9, 2019
  - /RelValTTbar_13/CMSSW_11_0_0_pre6-PU25ns_110X_upgrade2018_realistic_v3-v1/GEN-SIM-DIGI-RAW
  - size: 9000 events
  - code version: 712e6d6
  - EDM: /mnt/hadoop/store/user/jpata/RelValTTbar_13/pfvalidation/191004_163947/0000/step3_AOD*.root
  - flat ROOT: /storage/user/jpata/particleflow/data/TTbar/191009_155100/step3_AOD_*.root or /eos/user/j/jpata/particleflow/TTbar/191009_155100/step3_AOD_*.root
  - npy: /storage/user/jpata/particleflow/data/TTbar/191009_155100/step3_AOD_*.npz 

## Contents of the flat ROOT output ntuple

The TTree `pftree` contains the elements, candidates and genparticles:
- clusters ([PFRecCluster](https://github.com/cms-sw/cmssw/blob/master/DataFormats/ParticleFlowReco/interface/PFCluster.h))
  - `PFBlockElement::(ECAL, PS1, PS2, HCAL, GSF, HO, HFHAD, HFEM)`           
- tracks ([PFRecTrack](https://github.com/cms-sw/cmssw/blob/master/DataFormats/ParticleFlowReco/interface/PFRecTrack.h))
  - `PFBlockElement::TRACK` or `PFBlockElement::BREM`
- candidates ([PFCandidates](https://github.com/cms-sw/cmssw/blob/master/DataFormats/ParticleFlowCandidate/interface/PFCandidate.h))
- genparticles

```
pftree->Print()
*Br    0 :nclusters : nclusters/i                                            *
*Br    1 :clusters_iblock : clusters_iblock[nclusters]/i                     *
*Br    2 :clusters_ielem : clusters_ielem[nclusters]/i                       *
*Br    3 :clusters_ipfcand0 : clusters_ipfcand0[nclusters]/i                 *
*Br    4 :clusters_ipfcand1 : clusters_ipfcand1[nclusters]/i                 *
*Br    5 :clusters_ipfcand2 : clusters_ipfcand2[nclusters]/i                 *
*Br    6 :clusters_ipfcand3 : clusters_ipfcand3[nclusters]/i                 *
*Br    7 :clusters_npfcands : clusters_npfcands[nclusters]/i                 *
*Br    8 :clusters_layer : clusters_layer[nclusters]/I                       *
*Br    9 :clusters_depth : clusters_depth[nclusters]/I                       *
*Br   10 :clusters_type : clusters_type[nclusters]/I                         *
*Br   11 :clusters_energy : clusters_energy[nclusters]/F                     *
*Br   12 :clusters_x : clusters_x[nclusters]/F                               *
*Br   13 :clusters_y : clusters_y[nclusters]/F                               *
*Br   14 :clusters_z : clusters_z[nclusters]/F                               *
*Br   15 :clusters_eta : clusters_eta[nclusters]/F                           *
*Br   16 :clusters_phi : clusters_phi[nclusters]/F                           *
*Br   17 :ngenparticles : ngenparticles/i                                    *
*Br   18 :genparticles_pt : genparticles_pt[ngenparticles]/F                 *
*Br   19 :genparticles_eta : genparticles_eta[ngenparticles]/F               *
*Br   20 :genparticles_phi : genparticles_phi[ngenparticles]/F               *
*Br   21 :genparticles_x : genparticles_x[ngenparticles]/F                   *
*Br   22 :genparticles_y : genparticles_y[ngenparticles]/F                   *
*Br   23 :genparticles_z : genparticles_z[ngenparticles]/F                   *
*Br   24 :genparticles_pdgid : genparticles_pdgid[ngenparticles]/I           *
*Br   25 :ntracks   : ntracks/i                                              *
*Br   26 :tracks_iblock : tracks_iblock[ntracks]/i                           *
*Br   27 :tracks_ielem : tracks_ielem[ntracks]/i                             *
*Br   28 :tracks_ipfcand0 : tracks_ipfcand0[ntracks]/i                       *
*Br   29 :tracks_ipfcand1 : tracks_ipfcand1[ntracks]/i                       *
*Br   30 :tracks_ipfcand2 : tracks_ipfcand2[ntracks]/i                       *
*Br   31 :tracks_ipfcand3 : tracks_ipfcand3[ntracks]/i                       *
*Br   32 :tracks_npfcands : tracks_npfcands[ntracks]/i                       *
*Br   33 :tracks_qoverp : tracks_qoverp[ntracks]/F                           *
*Br   34 :tracks_lambda : tracks_lambda[ntracks]/F                           *
*Br   35 :tracks_phi : tracks_phi[ntracks]/F                                 *
*Br   36 :tracks_eta : tracks_eta[ntracks]/F                                 *
*Br   37 :tracks_dxy : tracks_dxy[ntracks]/F                                 *
*Br   38 :tracks_dsz : tracks_dsz[ntracks]/F                                 *
*Br   39 :tracks_outer_eta : tracks_outer_eta[ntracks]/F                     *
*Br   40 :tracks_outer_phi : tracks_outer_phi[ntracks]/F                     *
*Br   41 :tracks_inner_eta : tracks_inner_eta[ntracks]/F                     *
*Br   42 :tracks_inner_phi : tracks_inner_phi[ntracks]/F                     *
*Br   43 :npfcands  : npfcands/i                                             *
*Br   44 :pfcands_pt : pfcands_pt[npfcands]/F                                *
*Br   45 :pfcands_eta : pfcands_eta[npfcands]/F                              *
*Br   46 :pfcands_phi : pfcands_phi[npfcands]/F                              *
*Br   47 :pfcands_charge : pfcands_charge[npfcands]/F                        *
*Br   48 :pfcands_energy : pfcands_energy[npfcands]/F                        *
*Br   49 :pfcands_pdgid : pfcands_pdgid[npfcands]/I                          *
*Br   50 :pfcands_nelem : pfcands_nelem[npfcands]/I                          *
*Br   51 :pfcands_ielem0 : pfcands_ielem0[npfcands]/I                        *
*Br   52 :pfcands_ielem1 : pfcands_ielem0[npfcands]/I                        *
*Br   53 :pfcands_ielem2 : pfcands_ielem0[npfcands]/I                        *
*Br   54 :pfcands_ielem3 : pfcands_ielem0[npfcands]/I                        *
*Br   55 :pfcands_iblock : pfcands_iblock[npfcands]/I                        *
```

Distance matrix between elements:
```
root [2] linktree->Print()
*Tree    :linktree  : linktree for elements in block                         *
*Br    0 :nlinkdata : nlinkdata/i                                            *
*Br    1 :linkdata_distance : linkdata_distance[nlinkdata]/F                 *
*Br    2 :linkdata_iev : linkdata_iev[nlinkdata]/i                           *
*Br    3 :linkdata_iblock : linkdata_iblock[nlinkdata]/i                     *
*Br    4 :linkdata_ielem : linkdata_ielem[nlinkdata]/i                       *
*Br    5 :linkdata_jelem : linkdata_jelem[nlinkdata]/i                       *
```

Element to candidate association:
```
root [2] linkdata_elemtocand->Print()
*Br    0 :nlinkdata_elemtocand : nlinkdata_elemtocand/i                      *
*Br    1 :linkdata_elemtocand_iev :                                          *
*Br    2 :linkdata_elemtocand_iblock :                                       *
*Br    3 :linkdata_elemtocand_ielem :                                        *
*Br    4 :linkdata_elemtocand_icand :                                        *
```
## Numpy training ntuples

Produced using

```bash
python3 test/graph.py step3_AOD_1.root
```
- step3_AOD_1_ev.npz: PF elements, candidates, and the block associations via a numerical ID
  - elements: [Nelem, Nelem_feat] for the input PFElement data
  - element_block_id: [Nelem, ] for the PFAlgo-based block id
  - candidates: [Ncand, Ncand_feat] for the output PFCandidate data
  - candidate_block_id: [Ncand, ] for the PFAlgo-based block id 
- step3_AOD_1_dist.npz: sparse [Nelem, Nelem] distance matrix from PFBlockAlgo between the candidates
