# framework-comparison


### Symfony ###


*   **Artscapy dev team has more experience with Symfony**
    
*   Highly flexible/configurable and more decoupled (e.g. easier to switch to a different database or framework)
    
*   Dependency injection via constructor is the only way to provide depencencies to a class \*1
    
*   API Platform (extension which allows rapid REST/GraphQL API development and automatically generates OpenApi/Swagger documentation just by analysing your code)
    
*   Model/Entity object generally don’t exdend any other class and thus are lightweight and can be serialized when required (e.g. when storing in database for queued job worker to process in the background)

*   Great documentation & learning resources & support (symfony casts, Symfony Slack, Github)

*   EasyAdmin for admin panel

*   Front-end support via Encore (React, Vue, vanilla JS)
    
    
### Laravel ###

*   Larger community and more developers – easier to hire someone
    
*   Has more questions answered on Stackoverflow
    
*   Is less flexible/configurable and therefore easier to learn

*   Great documentation & learning resources & support (laracasts, Laravel Slack, Github)

*   Laravel Nova for admin panel

*   Front-end support via Laravel mix (React, Vue, vanilla JS)
    
  -------------------------------------------------------------------

\*1 Laravel can also use DI via constructor but it seems everywhere in the docs they use Facades and global helper functions which means it’s the recommended way. Using facades makes core business logic code tightly coupled to the framework, it makes it harder to swap and understand what depencencies a class has and it relies on correctness of phpdoc bloc to perform static analysis and/or debugging.

  

**Symfony Example:**

```php
class OrderProcessor {

  public function __constructor(
    private MailerInterface $mailer,
    private ModelManagerInterface $modelManager,
    private TemplateBuilder $templateBuilder,
    private ValidatorInterface $validator,
    private RouterInterface $router,
  ) 
  {}

  public function processOrder(string $orderId): Response
  {
    $order = $this->modelManager->findOrderById($orderId);

    $this->mailer->sendOrderConfirmation($order);

    return $this->templateBuilder->render('templates/order_processed.html');
  }
  
  public function doSomethingElse(): Response
  {
    $validator = $this->validator->make([
        'name' => 'required|max:255',
    ]);
 
    if ($validator->fails()) {
        return $this->router->redirect('/')
            ->withInput()
            ->withErrors($validator);
    }
 
    return $this->templateBuilder->render('templates/do_something_else.html');
   }

}
```

**Laravel Example:**

```php
class OrderProcessor {

  public function processOrder(string $orderId): Response
  {
    $order = Model::findOrderById($orderId)

    Mailer::sendOrderConfirmation($order);
    
    return view('templates/order_processed.html');
  }
  
  public function doSomethingElse(): Response
  {
    $validator = Validator::make([
        'name' => 'required|max:255',
    ]);
 
    if ($validator->fails()) {
        return redirect('/')
            ->withInput()
            ->withErrors($validator);
    }
 
    return view('templates/do_something_else.html');
   }

}
```
