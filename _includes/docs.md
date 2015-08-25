Codepot 2015 Workshop
=====================

Welcome to the Codepot 2015 Workshop!

Here you find all information required for you to work during the Codepot 2015 Workshop.

# Table of contents

- [Documentation](#documentation)
- [Ok what should I do now?](#ok-what-should-I-do-now)
- [When is my feature done?](#when-is-my-feature-done)
- [How to run my microservice locally](#how-to-run-my-microservice-locally)
- [Useful snippets / info](#useful-snippets-info)

# <a name="documentation"/></a> Documentation

## Good to do before the workshop

- [Read about Microservices](http://martinfowler.com/articles/microservices.html)
- [Watch a video about Microservices](https://www.youtube.com/watch?v=wgdBVIX9ifA)
- [Read about 12 Factor App](http://12factor.net/)
- [Read about Micro-Infra-Spring](https://github.com/4finance/micro-infra-spring/wiki)
- [Watch a video about Micro-Infra Spring](https://www.youtube.com/watch?v=D6V49K_Yb8g)
- [Read about Consumer Driven Contracts] (http://martinfowler.com/articles/consumerDrivenContracts.html)
- [Watch a video about Consumer Driven Contracts](https://vimeo.com/130779882)

### If you don't know Spring at all

- [Read a getting started to Spring Boot](https://spring.io/guides/gs/spring-boot/)

## Presentations and introductions

- [Codepot presentation](https://docs.google.com/presentation/d/1ZSMaZJrvurvH3-EKuI2DXifKenPVp2xq80uzJvr7yzs/edit?usp=sharing)
- [Micro-Infra spring fast introduction - online](https://docs.google.com/presentation/d/1xbdOWYvuGKnp-_1wGz-bZTobEbCePRr062YUnx4jTQg/edit?usp=sharing)
- [Micro-Infra spring fast introduction - pdf](resources/Microservices_Codepot.pdf)

# <a name="ok-what-should-I-do-now"/></a> Ok what should I do now? 

## Clone the GIT properties repository to your computer

Clone repositories from [https://github.com/Codepot-Microservices-2015-08](https://github.com/Codepot-Microservices-2015-08)

Run this command (if you have sent your SSH key to Github) to clone the properties repository

```
git clone git@github.com:Codepot-Microservices-2015-08/properties.git
```

And the appropriate service i.e. `butelkatr` for `red` team:

```
git clone git@github.com:Codepot-Microservices-2015-08/butelkatr-red.git
```

## Start coding!

Well, let your team pick a service and start coding. Once you commit and push,
Jenkins will build that for you and upload your JAR to nexus. Next Rundeck will
deploy your application to the apps server. 

Each of the microservices will have a predefined port at which it will listen
to requests so you don't have to worry about that.

The ports are presented below in the table.

### Reminder!

Try to be as reactive and non-blocking as possible!

# <a name="when-is-my-feature-done"/></a> When is my feature done?

The definition of done is as follows:

- the task has been successfully implemented
- you have tests that back that up
- you have created a metric to verify your feature
- your feature has production alerting in Seyren
- each team has its own Slack room with alerts popping up

# <a name="how-to-run-my-microservice-locally"/></a> How to run my microservice locally

It's all there in the readme - [boot-microservice-readme](https://github.com/4finance/boot-microservice)

# <a name="useful-snippets-info"/></a> Useful snippets / info

## Spring Rest Controller example

Example of a `Controller` with an endpoint at URL `/video` that accepts the `content-type` header equal to `application/vnd.some.service.v1+json`
and produces a `JSON` as output. You have to call it via `POST` HTTP method

```
@RestController
@RequestMapping(value = "/video", consumes = "application/vnd.some.service.v1+json", produces = MediaType.APPLICATION_JSON_VALUE)
public class SomeController {

    private final VideoService videoService;

    @Autowired
    public SomeController(VideoService videoService) {
        this.videoService = videoService;
    }

    @RequestMapping(method = RequestMethod.POST)
    public Videos doSomeFancyStuff(@RequestBody SomeRequestBody someRequestBody) {
        return videoService.doSomethingAwesome(someRequestBody);
    }
}
```

## Service Rest Client example

Example of a component registered via `@Component` (you should use `@Configuration` to register beans) that uses
 `ServiceRestClient` to call other components.

```
@Component
public class VideoService {

    private final ServiceRestClient serviceRestClient;
    private final RetryExecutor retryExecutor;

    @Autowired
    public VideoService(ServiceRestClient serviceRestClient, RetryExecutor retryExecutor) {
        this.serviceRestClient = serviceRestClient;
        this.retryExecutor = retryExecutor;
    }

    public ListenableFuture<Videos> doSomethingAwesome(Ingredients ingredients) {
        return serviceRestClient.forService("someAliasFromMicroserviceDescriptor")
                .retryUsing(retryExecutor)
                .post()
                .withCircuitBreaker(withGroupKey(asKey("hystrix_group")))
                .onUrl("/someUrlToWhichYouWantToSendARequest")
                .body(ingredients)
                .withHeaders().contentType("application/vnd.some.other.service.v1+json")
                .andExecuteFor()
                .anObject()
                .ofTypeAsync(Videos.class)
    }
}
```


## Metrics setup example

```
@Component
class IngredientsAggregator {

    private final SomeService someService; 
    private final Meter someMeter;

    @Autowired
    IngredientsAggregator(MetricRegistry metricRegistry,  SomeService someService) {
        this.someService = someService;
        this.someMeter = metricRegistry.meter("name.of.a.meter.metric");
        setupMeters(metricRegistry);
    }

    private void setupMeters(MetricRegistry metricRegistry) {
        metricRegistry.register("name.of.the.gauge.metric", (Gauge<Integer>) () -> someService.getValueForGauge());
    }

    public void doSomethingMeaningful(long sample) {
        // do something and mark the metric
        someMeter.mark(sample);
        // do something else
    }

}

```

## How to configure alerting in Seyren
