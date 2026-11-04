# COMP3041-GroupJ-Assignment1 [Reflective Proposal]

## Proposal Overview & Introduction

Project Selene was selected as the reviewed application for the purposes of this reflective proposal first and foremost as it was developed solely by one of the group members within Group J to provide reproductive health awareness while accounting for the predatory and increasingly widespread privacy concerns seen within other applications in the market, therefore this reflective proposal provides us with a unique insight into the reasonings behind why it was built and the operational and ethical compromises that were accepted during the course of its development while mapping it against ACM’s and IEEE’s Code of Ethics (COE).
Project Selene is a standalone, offline, Android-only application engineered using Flutter and Dart and enforces strict local data authority thanks to various approaches including:

- Stripping internet permissions at compile-time (assert_no_network.sh)
- Forbidding cloud backups (android:allowBackup=”false”)
- Encrypting personal logs through the Android Keystore

Despite all of this, Selene suffers from an effect known as the “Decentralisation Paradox”; as Selene aims to eliminate cloud storage and corporate monetisation, the application transfers most, if not all of the legal, clinical, and operational burdens onto the users, and without the support provided by institutions and corporate entities through clinical validation and cooperation, automated error mitigation and legal funding, this transfer of responsibility introduce a myriad of vulnerabilities.

## Literature Review & Comparative Analysis

### Sociopolitical Landscape & Reproductive Surveillance

Following the overruling of Roe v. Wade (1973) in 2022 and the ruling in Dobbs v. Jackson Women’s Health Organization (2022) by the Supreme Court of The United States (SCOTUS), Period Tracking Applications (PTAs) are now seen as a potential risk by individuals living in countries and states where their digital data could be subpoenaed and used against them and as a potential means for state/government-sanctioned surveillance and criminal prosecution.

Zadushlivy et al. (2025) performed a qualitative analysis of commercial PTAs and found that corporate privacy policies fail systematically to protect users from third-party data brokers and monetisation schemes, and law enforcement subpoenas, and considering that consumer generated health data fall outside the classification of protected health information (PHI) under HIPAA, servers containing the aforementioned information are vulnerable to warrants and discovery by governments across the world.

Meanwhile, Kelly and Habib (2023) investigate the socio-political predicament of post-Roe v. Wade reproductive tech, reasoning that PTAs, despite their vital necessity for bodily autonomy and health literacy present a risk to users due to the digital artifacts generated which may be sought after due to state-level criminalisation of abortion.
Selene resolves the issues identified by Zadushlivy et al. (2025) by storing the data locally, however as inferred from Kelly and Habib (2023), circumventing the detection and intentionally concealing such information can adversely affect them in hostile environments where reproductive care is criminalised as they could potentially be framed as intentional evasion.

Furthermore, governmental or state entities with access to forensic extraction tools such as Cellebrite may bypass established security measures and access the SQLite database in plaintext form.

This goes against the following code of ethics clauses:

- ACM 1.2 “Avoid Harm” & IEEE I.1: This is due to the developer’s lack of implementation of counter-forensic defences (such as duress pins that wipe the software’s data) which may result in its users facing legal troubles despite the attack vector being recognised.

A note in regard to this issue in particular, IEEE I.1 states “to hold paramount, the safety, health and welfare of the public”, however this would result in non-compliance of ACM 2.3 “Know and respect existing rules pertaining to professional work”, particularly when aiming to ensure the health of the public goes in direct contradiction of state or governmental directives, this shows how the codes are silent on models where a state or governmental institute would be the adversary itself, in a scenario where bodily autonomy is being active criminalised.

### Regulatory Hot-Potato & Deliberate Performance Degradation

Another issue pertains to the developer’s decision to base period predictions solely on a Student-t Kalman filter (bayes_filter.dart) that is applied to historical cycle lengths and refit to the users own tracked cycles (through personalize.dart) while intentionally abstaining from using physiological biomarkers such as Basal Body Temperature (BBT), cervical mucus, and Progesterone test strips (PdG).

Based on the analysis of Bull et al. (2019) which involved the analysis of 612,613 ovulatory cycles across 124,648 users, they established that menstrual variability is regulated primarily through physiological irregularities, where the follicular phase shows a high natural variability while the luteal phase is biologically constrained. Accordingly, ovulation prediction through calendar-base statistical filters alone is solely unreliable.

To emphasise on this, Symul et al. (2019) found that only 24% of ovulations occur on textbook cycle days 14 through 15, and that short luteal phases occur in up to 20% of monitored cycles. Symul et al. (2019) further demonstrated that the accurate prediction of ovulatory windows is only viable through multi-modal Hidden Markov models that combine calendar dates with biomarkers such as cervical mucus and BBT.

Furthermore, as deliberated in Keutzer and Simonsson (2020), the classification of a software as class IIa under EU Medical Device Regulation (MDR 2017/745, Rule 11) and abiding by US FDA digital health guidelines are incredibly expensive and laborious, especially given the current manpower attached to Selene thus far, in a sense, justifying the deliberate removal of biomarkers from period predictions and retaining them solely for archival purposes to avoid undertaking Class II medical device certification.

This goes against the following code of ethics clauses:

- ACM 2.6 “Perform work only in areas of competence” & IEEE I.5: The developer is not a gynaecologist or endocrinologist, nor has he collaborated with any for the purposes of this project. Therefore, modelling human hormonal cycles, fertility markers, and drug-herb interactions without formal medical guidance violates the professional boundary of competence.
- ACM 2.4 “Accept and provide appropriate professional review”: The predictive model never underwent clinical trial or a review process; it solely relied on self-generated synthetic data (synthetic.py) to benchmark calibration which constitutes self-validation.

### The Exclusion of Neuroendocrine Diversity & Polyendocrine Metabolic Ovarian Syndrome (PMOS)

Hadley et al. (2021) identified in their systematic literature review that commercial PTAs consistently produce inaccurate predictions due to their underlying reductionist frameworks that assumes regular ovulatory patterns. Furthermore, Hadley et al. (2021) noted that users experience severe emotional distress and heightened anxiety due to worries regarding unintended pregnancies and alienation when their bodies deviate from what is stated when PTAs provide inaccurate menstruation dates or fertile windows.

Project Selene sadly exhibits this exact algorithmic failing, the application implements an arbitrary refusal cut-off (in intervals.dart) where if a user’s Bayesian credible interval widens beyond two (2) weeks, the engine refuses to output a predicted window and returns a PredictionResult.tooIrregular exception.

Rodriguez et al. (2020) revealed that high cycle variance, phenotypic markers, and extended intervals can be modelled effectively through probabilistic networks and were able to allow trackers to remain functional for individuals afflicted with PMOS, formerly known as Polycystic Ovarian Syndrome (PCOS). This was done through the evaluation of predictive features within digital health trackers and highlights the issue where Selene perceives biological variance as an inconvenient outlier to be discarded, leaving individuals with PMOS, endometriosis and other neuroendocrinological syndromes without support.

This goes against the following code of ethics clauses:

- ACM 1.4 “Be fair and take action not to discriminate” & IEEE I.1, II.7: The developer has disenfranchised individuals with medical conditions and has therefore clearly failed the mandate of impartial design.

### Open Source Development Work & Liability Disclaimers

Project Selene is currently reliant on a single maintainer, and despite the open-source distribution model being seen as a more transparent alternative to proprietary healthcare, mobile health (mHealth) software and PTAs, several safety liabilities become apparent when they lack sustainable institutional oversight.

Following a systematic literature review conducted by Aljedaani and Babar (2021) on the challenges in developing secure mHealth applications reveals that small-scale, independent mHealth development environments systematically suffer from:

1. An absence of motivation or ethical considerations
2. Minimal user involvement during the development process
3. Inadequate developer expertise and knowledge

Aljedaani and Babar (2021) emphasised the importance of mobile app security through the application of any of the various security guidelines such as Open Web Application Security Project (OWASP).
Meanwhile, Gelinas et al. (2023) demonstrated that health applications routinely use exculpatory language, broad liability limitations, and blanket warranty disclaimers (such as “AS IS” clauses) and furthermore demonstrates that while developers develop and promote their application using clinical, medical and technical terminologies, they legally insulate themselves from any liability if any of the myriad issues such as algorithmic inaccuracies, data loss or software malfunctions inflict harm. This transfers the risk onto the user and the stakeholders while removing the possibility of enforcement or dispute resolution.

This is shown in Project Selene with its use of clinical vocabulary such as “Follicular”, “Ovulatory”, “Menstrual Phase”, “Drug Interaction Checker” and the MIT open-source license as stated below to abandon professional accountability while providing a service:

> THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND… IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

This goes against the following code of ethics clauses:

- ACM 1.4 “Be fair and take action not to discriminate”, ACM 1.6 “Respect privacy” & IEEE I.5: The lack of a private mechanism for grievance resolution is a flagrant violation especially given the sensitive nature of Project Selene, further exacerbated by the disclaiming of all liability behind an “AS IS” open-source license.

A note on this issue is that even in an attempt to resolve either ACM 1.4 or 1.6, the other clause would be non-compliant due to how Selene’s inherent architectural design in which no personal data is collected at all, thus requiring all the information needed for a bug report or grievance resolution to be provided voluntarily via the user which may very well be uncomfortable for them.

## Application of Ethical Theories

Two ethical principles and theories are applied to evaluate Project Selene beyond what was previously considered:

1. Deontology; Deontology is driven by the intent and the action instead of the consequences of what took place, formulated through the Categorical Imperative.
    - The developer’s active decision to ignore biomarker inputs to circumvent Class II MDR violates the formula of humanity as suppressing clinical variables that are effectively known to determine ovulatory biology strip the users of the truth and misleads them.
    - The developer’s active decision to release mHealth software while using an MIT license to nullify all legal obligations cannot be universalised, as if every developer had done that then the concept of medical software would implode due to the lack of check and balances.

2. Ethics of Care; The Ethics of Caring postulates that human beings are dependent upon each other and are capable of caring for others to an extent.
     - In an effort to provide the users with a local mHealth PTA app, the developer offloads all the operational and legal risks onto the user, effectively abandoning them, especially when and if the application needs to be updated.
