# Third-Party Data

This repository bundles or downloads several third-party artifacts
that are **not** covered by this repository's MIT code license (see
`LICENSE`). Their original sources and licensing terms are as follows.

## 1. ASHRAE 90.1-2019 commercial prototype building models (IDF files)

- **Files:** `data/idf_referans/*.idf` (the 5 tested-building reference
  IDFs, Buffalo/5A) and `data/idf_referans/ashrae901_2019_prototypes/*.zip`
  (the original 16-building prototype archives used to build the LOBO
  pool).
- **Source:** U.S. Department of Energy / Pacific Northwest National
  Laboratory, "Commercial Building Prototypes Based on ANSI/ASHRAE/IES
  Standard 90.1-2019 Appendix G PRM" (PNNL-32815); publicly available
  from the DOE Building Energy Codes Program at
  <https://www.energycodes.gov/prototype-building-models>. The
  original prototype concept is due to Deru et al. (NREL/TP-5500-46861,
  2011).
- **License/terms:** Produced by the U.S. Department of Energy and its
  national laboratories; publicly released for unrestricted research
  and simulation use via the above DOE portal. No separate copyright
  restriction is asserted by the authors of this repository. Users
  should consult the DOE Building Energy Codes Program site for the
  authoritative terms of use.

## 2. EnergyPlus weather files (EPW)

- **Files:** `data/epw_iklim/5A_Buffalo.epw`,
  `data/epw_iklim/1A_Miami.epw`,
  `data/epw_iklim/7_InternationalFalls.epw`.
- **Source:** TMY3 (Typical Meteorological Year 3) files distributed
  through the official EnergyPlus weather data repository,
  <https://energyplus-weather.s3.amazonaws.com/> (mirrored from
  <https://www.energyplus.net/weather>), specifically:
  - `USA_NY_Buffalo.Niagara.Intl.AP.725280_TMY3` (climate zone 5A)
  - `USA_FL_Miami.Intl.AP.722020_TMY3` (climate zone 1A)
  - `USA_MN_International.Falls.Intl.AP.727470_TMY3` (climate zone 7)
- **License/terms:** TMY3 data is derived from U.S. National Solar
  Radiation Database / National Climatic Data Center records and is
  distributed by the U.S. Department of Energy's EnergyPlus project
  free of charge for building energy simulation use. See
  <https://www.energyplus.net/weather> for the authoritative terms of
  use; this repository does not modify the underlying weather records.

## 3. EnergyPlus IDD schema

- **File:** `data/Energy+.idd`.
- **Source:** Bundled with the EnergyPlus simulation engine
  (U.S. Department of Energy / National Renewable Energy Laboratory
  and University of Illinois / Ernest Orlando Lawrence Berkeley
  National Laboratory), <https://energyplus.net>.
- **License:** EnergyPlus (including its IDD schema file) is
  distributed under the EnergyPlus open-source license (a BSD-style
  license); see the license terms bundled with any official EnergyPlus
  release or at <https://github.com/NREL/EnergyPlus/blob/develop/LICENSE.txt>.
  This file is included here only for reference/reproducibility of the
  comment-anchored parsing described in the manuscript; it is not
  modified.

## 4. EnergyPlus simulation engine

- **Version used for reported simulations:** EnergyPlus 22.1.0
  (matching the native `Version,22.1;` tag of the reference IDFs).
- **Source:** <https://github.com/NREL/EnergyPlus/releases>.
- **License:** EnergyPlus open-source license (BSD-style); see
  <https://github.com/NREL/EnergyPlus/blob/develop/LICENSE.txt>.
  EnergyPlus itself is not redistributed in this repository; Sprint 1
  downloads it from the official NREL/EnergyPlus GitHub releases page
  at run time.

## 5. LLM API access

- **Models:** `llama-3.3-70b-versatile` (Meta Llama 3 architecture) and
  `openai/gpt-oss-120b` (OpenAI open-weight model), both accessed via
  the Groq Inc. hosted API (<https://groq.com>).
- **Terms:** Use of these hosted endpoints is subject to Groq's API
  terms of service and the respective model providers' license terms
  (Meta Llama 3 Community License; OpenAI gpt-oss usage terms). No
  model weights are redistributed in this repository; only this
  study's own prompts, raw JSON responses, and derived parameter
  vectors are included.
