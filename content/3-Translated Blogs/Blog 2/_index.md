---
title: "Blog 2"
weight: 2
chapter: false
pre: "<b> 2. </b>"
---

# From Beta to Breakthrough

## From Beta to Breakthrough

From pilot to breakthrough: Scaling AI in healthcare from POC to real-world operations to create practical impact every day.

*Authors: Sim Yuanwei Aaron, Hardeep Arora*
*Published: May 6, 2025*

**Categories:** [Amazon Bedrock](https://aws.amazon.com/blogs/publicsector/category/artificial-intelligence/amazon-machine-learning/amazon-bedrock/), [Artificial Intelligence](https://aws.amazon.com/blogs/publicsector/category/artificial-intelligence/), [Foundation models](https://aws.amazon.com/blogs/publicsector/category/artificial-intelligence/generative-ai/foundation-models/), [Generative AI](https://aws.amazon.com/blogs/publicsector/category/artificial-intelligence/generative-ai/), [Healthcare](https://aws.amazon.com/blogs/publicsector/category/public-sector/healthcare-public-sector/), [Partner solutions](https://aws.amazon.com/blogs/publicsector/category/post-types/partner-solutions/), [Public Sector](https://aws.amazon.com/blogs/publicsector/category/public-sector/)

## Introduction:

[Generative AI](https://aws.amazon.com/ai/generative-ai/) is redefining the concept of preventive healthcare by delivering personalized, highly applicable, and flexible experiences. These experiences help bridge the gap between traditional clinical advice and infrequent consultations, thereby supporting individuals in making healthier lifestyle choices every day. As healthcare systems globally expand from "**sick care**" to include "**well care**", these emerging technologies promise tremendous potential in engaging, motivating, and empowering people to proactively manage their health.

However, transitioning AI application solutions from **proof of concept (POC)** to operations is not a simple task. Beyond overcoming both technical and non-technical challenges commonly encountered in any platform when moving to production, this process requires careful design, validation from stakeholders and users, along with continuous focus on end-user experience based on evidence-based behavioral medicine foundations.

In this post, we will discuss **Health Kaki**, a health companion using generative AI with [foundation models (FM)](https://aws.amazon.com/what-is/foundation-models/) on [Amazon Bedrock](https://aws.amazon.com/bedrock/), and highlight how the team overcame challenges in scaling from concept to real-world deployment.

## Health Kaki: A health companion, just for me

**Health Kaki** (with 'kaki' derived from Malay, meaning 'close friend' or 'companion') is a generative AI platform designed to empower individuals to be proactive in their health and lifestyle choices through personalized digital interactions. [The PoC](https://temus.com/case-study/health-kaki-a-genai-powered-resident-health-companion/) of Health Kaki [was co-developed](https://www.synapxe.sg/media-releases/innovation/synapxe-new-innovation-lab) by Synapxe and Temus with support from [Amazon Web Services (AWS)](https://aws.amazon.com/) and consultation from Singapore's Health Promotion Board (HPB) for Singapore's Ministry of Health (MOH), to support the [HealthierSG](https://www.healthiersg.gov.sg/) healthcare plan.

The platform harnesses the power of AI tools using Amazon Bedrock and Anthropic's **Claude 3.5 Sonnet** [large language model (LLM)](https://aws.amazon.com/what-is/large-language-model/) to create personalized nutrition and exercise plans from rich resources and information across Singapore's public healthcare ecosystem. These plans align with users' health goals, cultural preferences, and clinical recommendations.

What makes this technology powerful is that today it can generate countless permutations of choices and information to promote a healthier lifestyle.

For example, users can input a natural language command:

> Today I want to do Meatless Monday. Health Kaki, change this suggested meal from chicken to tofu... oh and adjust the cooking time and nutritional information so I know my macros are covered.

And Health Kaki generates a personalized meal plan and recommendations, as shown in the following screenshot.

**Figure 1. Health Kaki's personalized meal plan and recommendations**

Or, users can input this command:

> Health Kaki, I stayed up late working yesterday, can you find a yoga class near me? By the way, is that class eligible for discounts with the [PAssion Card](https://www.onepa.gov.sg/passion-card/passion-card-membership/passion-card-faqs) at my local Community Center?

And Health Kaki provides a personalized exercise plan and recommendations, as shown in the following screenshot.

**Figure 2. Health Kaki's personalized exercise plan and recommendations**

The following screenshot shows a question-and-answer exchange between a user and Health Kaki on the app. The chatbot feature allows users to ask about diet and exercise, as well as update their preferences.

**Figure 3. Health Kaki Assistant**

In summary, the long-sought goal of delivering the right intervention, to the right person, at the right time is finally within reach with innovations in LLMs and generative AI.

## Designing the solution correctly, from the start

The excitement of diving into new technologies for immediate experimentation is very appealing. After all, isn't that the essence of innovation and agile spirit? We agree that approach has value and is appropriate at certain times and circumstances. However, if the real goal is to scale an AI solution from PoC to operations, then a different approach will have a higher chance of success.

Instead of rushing to build solutions or letting current technology lead the way, you should focus on deeply understanding needs, current processes, and constraints. From there, identify the core features and functions that will make the solution truly effective. Consider the following factors:

* **Viability:** One of the key aspects when transitioning from PoC to operations is validating its viability, which stems from the problem you're solving. Specifically: How large is that problem, and how does this solution address its root cause? The market is not lacking digital applications and platforms providing generic health and disease management solutions. Most of them have failed, sometimes spectacularly. This becomes even more important in the context of LLMs and generative AI, where development, operation, and maintenance costs often lack clear precedents. Costs will increase significantly as the product scales and user interaction levels rise.

* **Evaluation and testing:** LLMs and generative AI are emerging and developing at unprecedented speed. Therefore, understanding system performance under various conditions is mandatory—and those conditions will change as the solution progresses from PoC, to proof of value, to operations. Health Kaki used scenario-based testing, metrics-based evaluation, and iterative validation to refine features and verify reliability. Metrics like **faithfulness scores** and **answer relevancy** provided valuable insights into model results. The closer to production phase, the more rigorous and varied the testing types become. Only with this multi-dimensional and sophisticated approach, rather than a one-size-fits-all safety testing process, can the solution balance innovation and safety.

* **Audience:** We need to move beyond outdated concepts of users and **user acceptance testing (UAT)**. Consider who actually uses the solution. Technologists need to recognize that in health decision-making and subsequent actions, there is no single "**user**". For example, a doctor may make health recommendations (like reducing sugar), a patient may agree that's an important goal, an insurance company or government may pay for the service, but the family caregiver is the one who decides what's for dinner. New technologies can also cause anxiety about the unknown or safety concerns for any of these stakeholders, and that needs to be considered too. Therefore, a digital healthcare solution must reconcile these complex and seemingly contradictory perspectives during evaluation.

To see this complexity more clearly: during testing, patients may say they don't need detailed guidance on reducing sugar. But when they're suddenly diagnosed with an acute condition or multiple comorbidities, they may change their minds. Our health and life circumstances are always changing, so what users need and value will change accordingly.

## Overcoming technical challenges

Like any innovation, there are countless challenges, including **known unknowns** and more importantly **unknown unknowns**. During Health Kaki's development, several technical challenges below were addressed using advanced **prompting** techniques and integrating a contextual knowledge base. Rigorous model evaluations, using standards like **faithfulness scores** and Large Model Systems Organization (LMSYS) rankings, showed that Anthropic's **Claude 3.5 Sonnet** model consistently delivered reliable output and good contextual awareness.

The team faced many technical challenges when developing Health Kaki, including:

* **Data quality and diversity:** The foundation for successful AI scaling is maintaining diverse, high-quality data sources that reflect the specific nuances of the population community. For Health Kaki, this meant handling Singapore's unique cultural and culinary context, including halal and vegetarian eating habits, traditional Chinese medicine influences, and diverse exercise preferences. To address this, the team adopted a human-AI hybrid approach. Data from trusted sources like the Health Promotion Board was enriched with **metadata** by LLMs. Human experts validated and refined this data to ensure cultural appropriateness and contextual accuracy. This rigorous process laid the foundation for creating customized recommendations that resonate with users.

* **Infrastructure and experience design:** Deploying AI solutions at scale requires a delicate balance between robust infrastructure and seamless user experience. For example, generative AI systems, while powerful, can cause computational latency, affecting real-time responsiveness. Health Kaki overcame this by implementing **progressive loading** strategies. These strategies provide users with engaging intermediate content while personalized results are being processed. Extensive user testing confirmed this approach, with participants appreciating the thoughtful UX design and clear progress indicators that minimize the feeling of waiting.

* **Tools balancing accuracy and scalability:** Choosing the right models and tools is absolutely essential. Claude 3.5 Sonnet from Anthropic's proven ability to balance personalization, scalability, and accuracy made it the foundation in Health Kaki's architecture. Metrics like usability, processing speed, and flexibility were analyzed to understand models' capabilities in handling ever-changing requirements and seamless integration into the Health Kaki platform.

* **Consistency in personalization:** For generative AI to build trust, recommendations must not only be deeply personalized but also consistently accurate. Maintaining this balance across interactions posed a significant challenge for Health Kaki.

## Guardrails

Implementing robust **guardrails** was an extremely important aspect of the Health Kaki project. These barriers serve as essential protective mechanisms, ensuring AI-generated health recommendations are always appropriate, safe, and reliable. The Health Kaki team approached this challenge by working closely with **subject matter experts** to identify a comprehensive set of parameters. These guardrails encompass various aspects, including dietary considerations, exercise guidance, health condition precautions, and lifestyle factors. By integrating these guardrails into the core of the recommendation engine, Health Kaki can deliver personalized health plans that are not only engaging but also appropriate for each user's unique health profile and needs. This approach demonstrates a commitment to responsible AI deployment in healthcare technology, balancing innovation with safety and overall user effectiveness.

## From PoC to impact: A detailed plan for success

Scaling AI solutions in healthcare requires more than technical capability; it demands a comprehensive approach combining **user-centric design**, solid technical capabilities, and **iterative validation processes**. Health Kaki's success in navigating these complexities has emphasized the importance of collaboration, careful planning, and continuous focus on users' complex network of needs.

As the project moves forward, insights and lessons learned from continuous user validation will continue to shape the platform's future development and expansion. The team's focus on scalability, **modular design**, and continuous improvement has positioned Health Kaki to evolve alongside emerging AI technologies, ultimately driving a comprehensive solution that can inspire and empower large populations to take daily steps toward healthier and happier lives.

[Try Amazon Bedrock](https://aistylist.awsplayer.com/) today and learn to [build your own generative AI application](https://docs.aws.amazon.com/bedrock/latest/userguide/getting-started.html).

**TAGS:** Artificial Intelligence, AWS Public Sector, AWS Public Sector Partners, healthcare, partner story

## About the authors

* **Sim Yuanwei Aaron**
    Aaron is a Partner Solutions Architect at AWS, focusing on collaboration with the public sector worldwide. He collaborates with AWS Partners to strengthen their capabilities and technical expertise in building effective cloud solutions for public sector customers. With dedication to enhancing partner capabilities, Aaron helps AWS Partners understand and implement cloud best practices, ensuring they can successfully deliver innovative solutions to government, education, healthcare, and non-profit organizations.

* **Hardeep Arora**
    Hardeep is Head of AI Engineering at Temus. He is an engineer with two and a half decades of experience in building and scaling data, analytics, and AI platforms. He has deep expertise in banking, consulting, and media sectors. Hardeep is a builder who strives to develop talent and improve people's lives by deploying AI solutions safely and securely. Hardeep holds a Bachelor's degree in Computer Science Engineering, an MBA specializing in Finance and Strategy, and a Post Graduate Certificate in Applied Statistics from the Indian Statistical Institute (ISI) in Kolkata.