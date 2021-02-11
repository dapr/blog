---
date: "2021-02-10T07:00:00-07:00"
title: "How the Dapr PHP SDK came to be"
linkTitle: "How the Dapr PHP SDK came to be"
author: "[Robert Landers](https://github.com/withinboredom)"
type: blog
---
 
Hello World, I'm Rob Landers, the maintainer of the new Dapr [PHP SDK]( https://github.com/dapr/php-sdk). Since I was a child, I've been writing software, and more importantly, I've been writing PHP since 2013. I work at Automattic on the Decision Science team, working with PHP, Python, JavaScript, and Scala. However, C# is my favorite general-purpose language since I first used it back in its 2.0 days. 

For me, language is a way to express a solution to a problem, and I love learning new languages and discovering exciting new solutions. Some languages are better suited than others to represent certain solutions, and I think that was the appeal of Dapr when I first discovered it. You can choose the language that best solves the problem with a homogenous, scalable framework.

## The path to a Dapr PHP SDK

The journey to create the PHP SDK technically started in 2006. I was in considerable debt after a motorcycle accident, and I was terrible at managing money.
 
Originally written in C#, I built some software that scraped my bank account and managed a budget via SMS messages. Over the years, I've tried turning it into a product, pitching it to investors, open-sourcing it, etc. No one was interested, but it didn't matter - I still needed it. Today, it runs on my laptop as nearly 30 microservices written in PHP and C#. At the beginning of 2020, I started investigating how to put it in the cloud.
 
I tried many things out before discovering Dapr and realizing that it could support any language with a bit of work. So, over a long weekend, I started putting it together. Thus, the PHP SDK was born while I migrated everything over to Dapr. The migration unlocked the path to adding some new, exciting features my wife has been asking for a long time - like the ability to ignore a purchase’s impact on the budget, or have some auto-categorization.
 
## Why PHP and Dapr?

PHP runs nearly [80% of the web](https://w3techs.com/technologies/details/pl-php). It's a web-native language best suited for doing "web stuff," whether that's running a blog, an online shop, or your app. There's a pretty good chance there's some PHP somewhere in any given organization.
 
Furthermore, many legacy applications are written in PHP (I've maintained a couple of them). There's a drive to keep adding more features and deliver more value, but there comes the point where some solutions are better written in another language, other than PHP. Microservices present a unique solution to this problem allowing developers to use the language and storage methodologies that best solve their challenges. However, when you have a large legacy application, microservices seem like a dreamland that you'll never be able to reach without significant management buy-in to rewrite everything from the ground up. This is where Dapr can offer a lot of value - Dapr building blocks let you interface with technologies that may be hard to integrate with PHP. With a Dapr PHP SDK, you can implement best practices and patterns without throwing away all of your legacy code.

## Examples

So how do Dapr API calls look like in PHP? Here are a few examples:

### State management

With the PHP SDK, you can easily define state with just a Plain Ole’ PHP Object: 
 
```php 
<?php
#[\Dapr\State\Attributes\StateStore('statestore', \Dapr\consistency\EventualLastWrite::class)]
class MyState {
    /**
     * @var string 
     */
    public string $string_value;
    
    /**
     * @var ComplexType[] 
     */
    #[\Dapr\Deserialization\Attributes\ArrayOf(ComplexType::class)] 
    public array $complex_type;
    
    /**
      * @var ComplexType
      */
    public ComplexType $object_type;
    
    /**
     * @var int 
     */
    public int $counter = 0;
 
    /**
     * Increment the counter
     * @param int $amount Amount to increment by
     */
    public function increment(int $amount = 1): void {
        $this->counter += $amount;
    }
}
 
$app = \Dapr\App::create();
$app->post('/my-state/{key}', function (
    string $key, 
    #[\Dapr\Attributes\FromBody]
    string $body, 
    \Dapr\State\StateManager $stateManager) {
        $stateManager->save_state(store_name: 'store', item: new \Dapr\State\StateItem(key: $key, value: $body));
        $stateManager->save_object(new MyState);
});
$app->get('/my-state/{key}', function(string $key, \Dapr\State\StateManager $stateManager) {
    return $stateManager->load_state(store_name: 'store', key: $key);
});
$app->start();
```

## Publish and subscribe

You can further decouple services by using a pub/sub pattern. To publish a message to a topic:
 
```php
<?php
$app = \Dapr\App::create();
$app->get('/publish', function(\DI\FactoryInterface $factory) {
    $publisher = $factory->make(\Dapr\PubSub\Publish::class, ['pubsub' => 'redis-pubsub']);
    $publisher->topic('my-topic')->publish(['message' => 'arrive at dawn']);
});
$app->start();
``` 

To subscribe and consume messages:

```php
$app = \Dapr\App::create(configure: fn(\DI\ContainerBuilder $builder) => $builder->addDefinitions([
    'dapr.subscriptions' => [new \Dapr\PubSub\Subscription('redis-pubsub', 'my-topic', '/receive-message')]
]));
$app->post('/receive-message', function(#[\Dapr\Attributes\FromBody] \Dapr\PubSub\CloudEvent $event) {
 // do something
});
$app->start();
```

For more code snippets, see the [Dapr PHP SDK repo]( https://github.com/dapr/php-sdk)

## Summary

From my perspective, Dapr is uniquely situated to assist with migrating from legacy applications to well-tested microservices. With Dapr, any language can integrate with it using HTTP. With SDKs, we can get rich integrations that improve the testability of the code we write and abstract the tricky bits. I invite you to try the SDK yourself, open issues on the SDK repo and share your experience on the [Dapr Discord server](https://aka.ms/dapr-discord). I'm really excited to see what you'll build with it!
