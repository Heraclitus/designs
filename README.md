# Purpose
This repo serves as a place to keep and present my designs and give you a better sense of what my capabilities are. 

## Who am I?  
I'm a professional software engineer working in the Seattle/Bellevue area since 2001. I graduated that same year from the University of Washington with a Bachelor of Science degree in Computing and Software Systems. That's as much detail as I'm going to provide about myself in a public setting. I will add that I've performed in a number of roles including Developer Lead, Principal, Manager and Software Architect. If you want more information please request a resume. 

## What soft skills do I have?
I still see myself as a hands on engineer. However, in the past 3-4 years I've developed talents working with more than one team, across organizational boundaries and with external companies to bring complex products from concept to reality.  My designs typically start with investigations and learning with product owners and specialty teams. From there I can gauge the level of buy in for solution themes and asses cultural trends before creating technical designs. Presenting those designs and taking feedback follows.  I am familiar with maintaining those designs as the product evolves and being the source of truth for my company and executive team. I'm also familiar with supporting my creations (and creations of others ) in stressful, time-sensitive circumstances where millions of dollars are on the line. Delegating and helping to bring the right people into a support engagement are familiar tasks. 

Management really **isn't** what gets me excited. It is something that I can do on an as-needed basis.  In that same vein I know that building a good team can be very challenging. I have experience building teams. I've haven't kept a count of how many candidates i've interviewed over the past 20 years. I'm confident it is in the multiple hundreds at this point. 

## What technical specialties do I have?
I won't bore you with the list of jargon words, you can get those from my official resume. I've focused most of my career on "backend services". I have at times dabbled in frontend but that isn't where my interests or proficiency exists. Many of my professional projects have been customer facing. Some with millions of clients connecting daily.  Around 2016 I started getting into Cloud offerings from AWS and container orchestration with Kubernetes. Next I became excited about ["Infrastructure as code"](https://en.wikipedia.org/wiki/Infrastructure_as_code) or IaC in 2018 with a focus on [Terraform](https://www.terraform.io/).  Being able to both write product software and create the infrastructure to deploy and run that software is very satisfying. The next big mountain to climb was incorporating a Service Mesh (ie. [Istio](https://istio.io/)) into a high profile customer facing product. Despite having my skill set expanded to system level, I continued to stay familiar with my teams' Golang code base. I often performed code reviews and took smaller development tasks. I delegated most of the heavy lifting code creation to my team. 

#### Can I deal with complex problems & systems?
The best way to answer this question may be to point you at a very challenging production outage I was asked to diagnose in the later portion of 2020 and bleeding over into 2021. This document is a deep dive into the architecture, symptoms, diagnosis, hypothesis, testing and finally resolution. I've removed the sensitive information and boiled down the effort a bit to make it easier to follow. As this issue unfolded it was very chaotic and included multiple late nights, weekends and days working with a team of software engineers and operations engineers. Take a look at my big challenge [part-1](https://github.com/Heraclitus/wiki/blob/master/aws/connection_behavior.md) & [part-2](https://github.com/Heraclitus/wiki/blob/master/aws/connection_behavior_2.md).
 

#### Can I work with Open Source projects?
* A good example would be an issue I documented for the Jaeger tracing product in github. Here you can see a thorough analysis into the project code base plus test creation to prove the issues existence. https://github.com/jaegertracing/jaeger/issues/2451. 
* Additionally an installation issue for a popular Service Mesh called Istio. https://github.com/istio/istio/issues/27564.


# Designs
I've somewhat recently decided to adopt [C4 Model](https://c4model.com/) as my design language of choice. I highly recommoned checking out Simon Brown's presentation on it. After struggling with my own attempts to express designs and being frustrated with the results I found that Simon's system is simple and effective.  I'll use it to show a couple of designs I used in my recent job. Theses have been sanitized so as not to expose anything proprietary. My other go-to is PlantUML which I extend using the C4 Model PlantUML extension https://github.com/plantuml-stdlib/C4-PlantUML. 

Sadly, github [still doesn't have](https://github.community/t/support-uml-diagrams-in-markdown-with-plantuml-syntax/626/38) a good way to display PlantUML directly from markdown. 

## In Vehicle Multi-Model Cloud Connected App Design
![GenericVoiceDesign](https://user-images.githubusercontent.com/5532892/131043509-4dd73892-d136-4d1e-8b5f-7bc267ee9a33.png)

Below is a related design for specific partner that had a mediated NLU voice provider that obscured vehicle identity and required a more complex solution to disambiguate users in the same vehicle account and same Alexa account. 

<div align="center"><img src="https://github.com/Heraclitus/designs/blob/main/docs/img/voice.png" height="1000"/></div>

### Below is the PlantUML/C4 Model source for that image.
<details>
 <summary>Click to show source</summary>

 ```
@startuml
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml

Person(driver, "Vehicle Operator", "A person owning and driving a car")
System_Ext(ihu, "HeadUnit-ICE", "Allows customer to view and control in-car applications")
System_Boundary(c1, "Service Cloud") {
    Container(langer, "Langer - Voice Service", "Receives textual voice results & Produces follow up Intent & Slots")
    Container(vbs, "VBS - Voice Backend Service", "Receives textual voice results & Produces follow up Intent & Slots. Also, resolves user/vehicle identity ambiguity")
    Container(inf, "Inference Service", "Slot & intent arbitration")
    Container(dialogue, "Dialogue Manager Service", "Application State and execution arbitration")
    Container(3rdPartyApp, "Specific Third Party App Service", "Some specific 3rd Party App like Yelp!")    
}

System_Ext(vprovider, "Voice Provider", "NLU Speech-to-text & Text-to-speech")
System_Ext(3rdParty, "3rdParty", "Example 3rd Party API")

Rel(vprovider, vbs, "Invokes alexa.proto w/text-from-speach", "HTTPS1.1/2 JSON")
Rel(vprovider, langer, "Invokes w/text-from-speach", "HTTPS1.1/2 JSON")
Rel(driver, ihu, "Interacts with", "Voice/Fingers")
Rel(ihu, vprovider, "Invokes API calls for NLU Processing", "HTTPS2-Multipart")
Rel(langer, dialogue, "Invokes", "HTTPS/2 GRPC")
Rel(langer, inf, "Invokes", "HTTPS/2 GRPC")
Rel(inf, 3rdPartyApp, "Invokes", "HTTPS/2 GRPC")
Rel(3rdPartyApp, 3rdParty, "Invokes", "HTTPS/1.1 REST/JSON")
Rel(ihu, vbs, "Invokes API calls for matchup and UI screen match", "HTTPS1.1-JSON")
SHOW_LEGEND()
@enduml
```

 </details>

### Below is a design for handling multiple application versions for a Vehicle based e-commerce solution

![MultiVersionDesign](https://user-images.githubusercontent.com/5532892/131046205-68b685b4-1702-4465-b2d3-fe1ec1a409d7.png)

**and the PlantUML source provided below ...**

<details>
 <summary>Click to show source</summary>

```
@startuml
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml

UpdateRelStyle(black, black)

System_Boundary(drivers, "Drivers") {
  Person(driverWhite, "Driver with 1.X - Legacy HeadUnit")
  Person(driverGreen, "Driver with 2.1.[5-9] and 2.X HeadUnit")
  Person(driverBlue, "Driver with 2.1.[0-4] HeadUnit")
}

AddElementTag("egreen", $fontColor="green", $borderColor="green")
AddElementTag("ewhite", $fontColor="white", $borderColor="white", $bgColor="black")
AddElementTag("eblue", $fontColor="blue", $borderColor="blue")

System_Boundary(c1, "Cloud Services") {
    System(modernBackend, "Modern Backend", "Public membrane service for modern headunits")
    System(legacyBackend, "Legacy Backend", "Public membrane service for legacy headunits")

    System(ambassador, "Ambassador API Gateway") {        
    }

    System_Boundary(c6, "Inference Manager and Registry Unit") {
      System(infer, "Inference Manager", "App State user workflow management")
      System(registry, "Registry Service", "Knows which UI layout file relates to which app by name")
    }

    System_Boundary(c5, "UI Format Translation Farm") {
      System_Boundary(translationWhite, "UI Format Translation Service White", $tags="ewhite")
      System_Boundary(translationBlue, "UI Format Translation Service Blue", $tags="eblue")
      System_Boundary(translationGreen, "UI Format Translation Service Green", $tags="egreen")
    }

    System_Boundary(c7, "3rd Party App Farm") {
      System_Boundary(3rdpartyAppsWhite, "3rd Party Apps Legacy White: Many", $tags="ewhite") {
      }
      System_Boundary(3rdpartyAppsGreen, "3rd Party Apps Modern Green: Many", $tags="egreen") {
      }
      System_Boundary(3rdpartyAppsBlue, "3rd Party Apps Modern Blue BOR: Many", $tags="eblue") {
      }
    }
}

System_Ext(CDN, "CDN", "Fast distributed content network")


AddRelTag("white", $lineColor="black")
AddRelTag("green", $lineColor="green")
AddRelTag("blue", $lineColor="blue")

Rel(driverGreen, ambassador, "1.0 Green calls w/app name", "HTTPS", $tags="green")
Rel(driverBlue, ambassador, "1.0 Blue calls w/app name", "HTTPS", $tags="blue")
Rel(driverWhite, ambassador, "1.0 White calls w/app name", "HTTPS", $tags="white")

Rel(ambassador, modernBackend, "1.1 Green proxies w/app name", "HTTPS", $tags="green")
Rel(ambassador, modernBackend, "1.1 Blue proxies w/app name", "HTTPS", $tags="blue")
Rel(ambassador, legacyBackend, "1.1 White proxies w/app name", "HTTPS", $tags="white")

Rel(modernBackend, infer, "1.2. Green calls w/app name", "GRPC", $tags="green")
Rel(modernBackend, infer, "1.2. Blue calls w/app name", "GRPC", $tags="blue")
Rel(legacyBackend, infer, "1.2. White calls w/app name", "GRPC", $tags="white")

Rel(infer, registry, "1.3 Get app name endpoint location", "GRPC", $tags="white")
Rel(infer, registry, "1.3 Get app name endpoint location", "GRPC", $tags="blue")
Rel(infer, registry, "1.3 Get app name endpoint location", "GRPC", $tags="green")
Rel(infer, 3rdpartyAppsGreen, "1.4 Invoke specific green 3rd Party app", "GRPC", $tags="green")
Rel(infer, 3rdpartyAppsBlue, "1.4 Invoke specific blue 3rd Party app", "GRPC", $tags="blue")
Rel(infer, 3rdpartyAppsWhite, "1.4 Invoke specific white 3rd Party app", "GRPC", $tags="white")

Rel(modernBackend, translationGreen, "1.5 sends modern UI format", "Uses", "GRPC", $tags="green")
Rel(modernBackend, translationBlue, "1.5 sends modern UI format", "Uses", "GRPC", $tags="blue")
Rel(legacyBackend, translationWhite, "1.5 sends non-modern UI format", "Uses", "GRPC", $tags="white")

Rel_U(driverWhite, CDN, "2.0 loads text, icons, imgs", "HTTPS", $tags="white")
Rel_U(driverBlue, CDN, "2.0 loads text, icons, imgs", "HTTPS", $tags="blue")
Rel_U(driverGreen, CDN, "2.0 loads text, icons, imgs", "HTTPS", $tags="green")

SHOW_LEGEND(false)
@enduml
```

 </details>
