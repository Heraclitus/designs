# Purpose
This repo serves as a place to keep and present my designs and give you a better sense of what my capabilities are. 

## Who am I?  
I'm a professional software engineer working in the Seattle/Bellevue area since 2001. I graduated that same year from the University of Washington with a Bachelor of Science degree in Computing and Software Systems. That's as much detail as I'm going to provide about myself in a public setting. I will add that I've performed in a number of roles including Developer Lead, Principal, Manager and Software Architect. If you want more information please request a resume. 

## What soft skills do I have?
I still see myself as a hands on engineer. However, in the past 3-4 years I've developed talents working with more than one team, across organizational boundaries and with external companies to bring complex products from concept to reality.  My designs typically start with investigations and learning with product owners and specialty teams. From there I can gauge the level of buy in for solution themes and asses cultural trends before creating technical designs. Presenting those designs and taking feedback follows.  I am familiar with maintaining those designs as the product evolves and being the source of truth for my company and executive team. I'm also familiar with supporting my creations (and creations of others ) in stressful, time-sensitive circumstances where millions of dollars are on the line. Delegating and helping to bring the right people into a support engagement are familiar tasks. 

Management really **isn't** what gets me excited. It is something that I can do on an as-needed basis.  In that same vein I know that building a good team can be very challenging. I have experience building teams. I've haven't kept a count of how many candidates i've interviewed over the past 20 years. I'm confident it is in the multiple hundreds at this point. 

## What technical specialties do I have?
I won't bore you with the list of jargon words, you can get those from my official resume. I've focused most of my career on "backend services". I have at times dabbled in frontend but that isn't where my interests or proficiency exists. Many of my professional projects have been customer facing. Some with millions of clients connecting daily.  Around 2016 I started getting into Cloud offerings from AWS and container orchestration with Kubernetes. Next I became excited about ["Infrastructure as code"](https://en.wikipedia.org/wiki/Infrastructure_as_code) or IaC in 2018 with a focus on [Terraform](https://www.terraform.io/).  Being able to both write product software and create the infrastructure to deploy and run that software is very satisfying. The next big mountain to climb was incorporating a Service Mesh (ie. [Istio](https://istio.io/)) into a high profile customer facing product. 


#### Can I deal with complex problems & systems?
The best way to answer this question may be to point you at a very challenging production outage I was asked to diagnose in the later portion of 2020 and bleeding over into 2021. This document is a deep dive into the architecture, symptoms, diagnosis, hypothesis, testing and finally resolution. I've removed the sensitive information and boiled down the effort a bit to make it easier to follow. As this issue unfolded it was very chaotic and included multiple late nights, weekends and days working with a team of software engineers and operations engineers. Take a look at my big challenge [part-1](https://github.com/Heraclitus/wiki/blob/master/aws/connection_behavior.md) & [part-2](https://github.com/Heraclitus/wiki/blob/master/aws/connection_behavior_2.md).
 

#### Can I work with Open Source projects?
* A good example would be an issue I documented for the Jaeger tracing product in github. Here you can see  thorough analysis into the project code base plus test creation to prove the issues existence. https://github.com/jaegertracing/jaeger/issues/2451. 
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
