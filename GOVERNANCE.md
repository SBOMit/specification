# SBOMit project governance
This document covers the project's governance and committer process.  The
project consists of the 
[SBOMit specification](https://github.com/SBOMit/specification), related documentation, and
implemenations hosted under the organization.

## Code of Conduct

The SBOMit community abides by the Open Source Software Foundation's [code of conduct](/CODE-OF-CONDUCT.md). An excerpt follows:

> We as members, contributors, and leaders pledge to make participation in our community a harassment-free experience for everyone, 
> regardless of age, body size, visible or invisible disability, ethnicity, sex characteristics, gender identity and expression, 
> level of experience, education, socio-economic status, nationality, personal appearance, race, caste, color, religion, or sexual 
> identity and orientation. 

SBOMit community members represent the project and their fellow contributors. We value our 
community tremendously, and we'd like to keep cultivating a friendly and collaborative environment 
for our contributors and users. We want everyone in the community to have a positive experience.

We also interact closely with other open source groups and projects, most notably the [CNCF project](https://www.cncf.io/) 
[in-toto](https://in-toto.io).  Maintaining a productive and healthy collaborative relationship with projects hosted at other open 
source foundations is also a major goal.


## Maintainership and Consensus Builder
The project is maintained by the people indicated in
[MAINTAINERS.txt](MAINTAINERS.txt).  A maintainer is expected to (1) submit and
review GitHub pull requests and (2) open issues.
A maintainer has the authority to approve or reject pull requests submitted by
contributors.  Any maintainer may also interact with the OpenSSF TAC on behalf of the
project.  


## Specification Contributions
A contributor can submit GitHub pull requests to the project's repositories.
They must follow the project's [code of
conduct](CODE-OF-CONDUCT.md) and the [Developer Certificate of
Origin (DCO)](https://developercertificate.org/).  Submitted pull
requests undergo review for the following required items:

* Checks for *Signed-off-by* commits via [Probot: DCO](https://probot.github.io/apps/dco/)
* Review by one or more [maintainers](MAINTAINERS.txt)

Note that the depth of maintainer review will depend on the scope of the change.
A minor clarification or typo may be approved by a single maintainer, while a 
change in project scope or a design change, will involve a discussion period
where all maintainers have time to review and participate.

At this time, we do not formalize the process of proposing and accepting changes, 
but expect as the specification matures that this will be added as is needed.
We likely will model in-toto's ITE process to keep strong alignment with that 
project.

## Changes in maintainership
Active contributors may be offered or request to be granted maintainer status.
This requires approval from a 2/3 majority of currently voting maintainers with at
least a 72 hour public voting period.

Maintainers may be moved to emeritus status.  This is done at the request of the
maintainer moving to emeritus at any time.  Alternatively, moving a maintainer to
emeritus status may be proposed by any maintainer and will be passed with a 2/3 
majority of voting maintainers with at least a 72 hour public voting period.  
Emeritus maintainers are listed in the MAINTAINERS.txt file as acknowledgment for 
their prior service to the project, but no longer have code review, voting, or other 
maintainer privileges for the project.

## Changes in governance
The maintainers supervise changes in governance.  Changes are approved by a 2/3 
majority of voting maintainers with a 72 hour public voting / discussion period.
