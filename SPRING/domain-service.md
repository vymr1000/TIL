# Domain Service & Application Service

## **Domain services & Application services**

*It is often said that domain services carry domain knowledge that doesn’t naturally fit entities and value objects. There’s another reason, however, why you may want to introduce a domain service. That reason is related to domain model isolation.*        
도메인 서비스는 ***entity***와 ***value object(VO)*** 에 자연스럽게 맞지 않는 도메인 정보를 전달한다고 한다. 그러나 도메인 서비스를 도입하려는 또 다른 이유가 있다. 그 이유는 도메인 모델 **격리(Isolation)** 와 관련이 있다.

*So, what differs domain services from application services? Both these concepts assume stateless classes which can work on top of domain entities and value objects, but that’s pretty much as far as their similarities go. The main difference between them is that **domain services hold domain logic whereas application services don’t.***       
그렇다면, 도메인 서비스와 애플리케이션 서비스의 차이점은 무엇일까. 이 두 개념은 도메인 엔티티와 ***value object*** 위에서 작동할 수 있는 **Stateless class**를 가정하지만, 유사성은 거의 동일하다. 이들의 주요 차이점은 **도메인 서비스는 도메인 로직을 유지하는 반면 애플리케이션 서비스는 그렇지 않다는 것이다.**

*Domain logic is everything that is related to business decisions. Domain services, therefore, participate in the decision-making process the same way entities and value objects do. And application services orchestrate those decisions the same way they orchestrate decisions made by Entities and Value objects.*     

도메인 로직은 비즈니스 의사 결정과 관련된 모든 것이다. 그러므로 도메인 서비스는 ***entity***와 ***value object*** 가 하는 것과 같은 방식으로 의사 결정 과정에 참여한다. 또한 애플리케이션 서비스는 ***entity*** 및 ***value object***의 의사 결정을 조정하는 것과 동일한 방식으로 이러한 의사 결정을 조정한다.

아래 애플리케이션 서비스의 예시를 보자.

```java
/* 현금 인출 메소드 */
public void WithdrawMoney(decimal amount)
{
		/* ATM 도메인로직 호출 부 */
    _atm.DispenseMoney(amount);
    decimal amountWithCommission = _atm.CalculateAmountWithCommission(amount);
 
		/* 비즈니스적 과정에 따른 결과 저장 */
    _paymentGateway.ChargePayment(amountWithCommission);
    _repository.Save(_atm);
}
```

*The `WithdrawMoney` method is part of an application service and comprises a customer-facing API. It tells an ATM entity to dispense some amount of money first, then asks it to calculate the amount of money with a commission, uses the calculation result to charge the payment through a payment gateway and finally saves the entity to the database.*       

이 `WithdrawMoney`방법은 애플리케이션 서비스의 일부이며 사용자 대면 API로 구성된다. ATM ***entity***에게 먼저 일정량의 돈을 출금하도록 지시한 다음 수수료로 금액을 계산하도록 요청하고 계산 결과를 사용하여 지불 게이트웨이를 통해 지불을 청구하고 마지막으로 ***entity***를 데이터베이스에 저장한다.

*In the first two lines, the method uses the `Atm domain entity` to make some business decisions. With the last two lines, it transforms those decisions into visible side effects. It calls the payment gateway and modifies the state of the database.*       

처음 두 줄에서 이 메서드는 `Atm domain Entity`를 사용하여 몇 가지 비즈니스 결정을 내린다. 마지막 두 줄을 사용하여 비즈니스적 과정의 결과물로 변환한다. 지불 게이트웨이를 호출하고 데이터베이스 상태를 수정하는 것이 그것이다.

*The first two lines here are about delegating the decision-making process to the domain model. But isn’t the sole act of knowing which two lines of code to use a domain knowledge in itself? Shouldn’t we extract it into a domain service?*      

여기에서 처음 두 줄은 의사 결정 프로세스를 도메인 모델에 위임하는 것이다. 하지만 어떤 두 줄의 코드는 그 자체로 도메인 지식을 사용하는 것이 아닌가? 도메인 서비스로 추출해야 하지 않을까?

```java
/* 변환 후 WithdrawMoney 메서드, 도메인에 대한 내용을 Domain Service에 위임한다 */
public void WithdrawMoney(decimal amount)
{
    decimal amountWithCommission = _atmService.DispenseAndCalculateCommission(
        _atm, amount);
 
    _paymentGateway.ChargePayment(amountWithCommission);
    _repository.Save(_atm);
}

/* Domain Service */
public sealed class AtmService
{
    public decimal DispenseAndCalculateCommission(Atm atm, decimal amount)
    {
        atm.DispenseMoney(amount);
        return atm.CalculateAmountWithCommission(amount);
    }
}
```

*Not really. The mere fact of using more than one line of code that is somehow related to the domain model doesn’t constitute a domain knowledge. What matters is whether or not those lines of code are responsible for making business decisions.*        

사실 그렇지 않다. 도메인 모델과 어떻게든 관련이 있는 두 줄 이상의 코드를 사용한다는 사실만으로 도메인 지식이 구성되지 않는다. 중요한 것은 이러한 코드 라인이 비즈니스 의사 결정에 책임이 있는지 여부이다.

*In the sample above, the `DispenseAndCalculateCommission` method has a cyclomatic complexity of 1. There are no branches (if statements) that signalize the code makes any decisions whatsoever, it just asks the domain entity to do two separate things.*        

위의 샘플에서 `DispenseAndCalculateCommission` 방법은 순환 복잡도가 1이다. 코드를 신호화하는 분기(문장이 어떤 결정을 내리더라도)는 없고 도메인 엔티티에 두 가지 개별적인 작업을 수행하도록 요청한다.

*The order in which these two methods are called doesn’t matter either. If we were to re-arrange them and put the commission calculation query above the dispense money command, nothing would change in terms of the Atm's invariants, it would still remain valid. It’s a strong sign there are no leaking implementation details either.*        

이 두 가지 방법이 호출되는 순서도 중요하지 않다. 만약 우리가 그것들을 다시 배열하고 수수료 계산 쿼리를 분배금 명령 위에 두면, ATM의 불변량 측면에서 아무것도 변하지 않을 것이고, 그것은 여전히 유효할 것이다. 구현 세부 정보가 유출되지 않았다는 것은 강력한 신호이기도 합니다.

이제 코드 샘플을 약간 변경하고 검증도 포함된다고 가정해 보자.

```java
public void WithdrawMoney(decimal amount)
{
    if (!_atm.CanDispenseMoney(amount))
        return ;
 
    _atm.DispenseMoney(amount);
    decimal amountWithCommission = _atm.CalculateAmountWithCommission(amount);
 
    _paymentGateway.ChargePayment(amountWithCommission);
    _repository.Save(_atm);
}
```

*The cyclomatic complexity in this example is higher than one because we now have an "if" statement. Doesn’t it mean the application service now contains domain knowledge?*        

이 예제의 순환 복잡도는 "if" 문장이 있기 때문에 1보다 높다. 이제 애플리케이션 서비스에 도메인 지식이 포함되어 있다는 뜻 아닐까?

*Also, no. The actual decision-making process still resides in Atm. The entity and entity alone decides whether it can dispense any money. The application service just orchestrates that decision and either continues the execution or not.*      

또한, 아닙니다. 실제 의사 결정 과정은 여전히 ATM에 존재한다. 기업이 돈을 지출할 수 있는지 여부는 기업과 기업만이 결정한다. 애플리케이션 서비스는 해당 결정을 조정하고 실행을 계속할지 여부를 결정합니다.

*As long as the DispenseMoney method in Atm has a pre-condition stating that CanDispenseMoney must hold true prior to dispensing cash, all invariants stay protected. The precondition itself can be implemented as simple as this:*        

ATM의 DispenseMoney 메서드에 현금을 지출하기 전에 CanDispenseMoney가 참이어야 한다는 전제 조건이 있는 한 모든 불변량은 보호 상태를 유지한다. 전제 조건 자체는 다음과 같이 간단하게 구현할 수 있다.

```java
public void DispenseMoney(decimal amount)
{
    if (!CanDispenseMoney(amount))
        throw new InvalidOperationException();
 
    /* ... */
}
```

*So, even if the application service ignores the decision made by CanDispenseMoney, the Atm entity will not enter an inconsistent state. It will throw an exception thus adhering to the fail fast principle.*      

따라서 애플리케이션 서비스가 캔디스팬스 머니의 결정을 무시하더라도 ATM 주체는 일관성이 없는 상태가 되지 않는다. 따라서 페일패스트 원칙을 고수하는 예외를 던질 것이다.

## 언제 Domain Service 를 따로 추출해야 하는가.

*The application service in the sample above doesn’t make any business decisions, it delegates those decision to the domain model. Note that the domain model is isolated: the Atm entity doesn’t save itself to the database and doesn’t directly charge payments through the payment gateway. We have a nice separation of concerns here: business logic is attributed to the domain model, while interactions with the external world - to the application service.*     

위의 샘플의 애플리케이션 서비스는 비즈니스 결정을 내리지 않고 도메인 모델에 결정을 위임한다. Atm 엔티티는 데이터베이스에 저장되지 않으며 지불 게이트웨이를 통해 직접 지불을 청구하지 않는다. 비즈니스 로직은 도메인 모델에 귀속되는 반면 외부 세계와의 상호 작용은 애플리케이션 서비스에 귀속된다.

*You can notice a pattern in most code bases that adhere to such a guideline. Their execution flow goes as follows:*        

대부분의 코드베이스에서 이러한 지침을 준수하는 패턴을 볼 수 있다. 실행 흐름은 다음과 같다.

- ***Step1,** Prepare all information needed for a business operation: load participating entities from the database and retrieve any required data from other external sources.*
- ***Step2,** Execute the operation. The operation consists of one or more business decisions made by the domain model. Those decisions result in either changing the model’s state, generating some artifacts (amountWithCommission value in the sample above), or both.*
- ***Step3,** Apply the results of the operation to the outside world.*

---

- ***Step1,*** 비즈니스 운영에 필요한 모든 정보 준비: 데이터베이스에서 참여 엔티티를 로드하고 다른 외부 소스에서 필요한 데이터를 검색한다.
- ***Step2,*** 작업을 실행한다. 작업은 도메인 모델에 의해 결정된 하나 이상의 비즈니스 결정으로 구성된다. 이러한 결정으로 인해 모델의 상태가 변경되고 일부 아티팩트가 생성된다(금액). 위의 샘플에서 커미션 값을 사용하거나 둘 다 사용한다.
- ***Step3,*** 작업 결과를 외부에 적용한다.

*Only the 1st and the 3rd steps involve the work with external dependencies. The 2nd step is closed under the data retrieved in the first step. The arguments it accepts and the output it generates consist of entities, value objects, and primitive types only.*

오직 1단계와 3단계만이 외부 의존성이 있는 작업을 포함한다. 2단계는 1단계에서 검색된 데이터에 따라 닫힌다. 인수가 허용하는 인수와 생성되는 출력은 엔티티, 값 개체 및 원시 유형으로만 구성된다.

*Note that in simple CRUD applications, there’s no 2nd step because there are no decisions to make. In this case, all operations can be performed solely by application services, no need to delegate them to a domain model. In fact, there can be no rich domain model whatsoever. An anemic domain model would work just fine in such situations.*

단순한 CRUD 애플리케이션에서는 결정을 내릴 수 없기 때문에 두 번째 단계가 없다. 이 경우 모든 작업은 애플리케이션 서비스만 수행할 수 있으므로 도메인 모델에 위임할 필요가 없다. 사실, ***Rich*** 도메인 모델은 있을 수 없다. ***Anemic*** 도메인 모델은 그러한 상황에서 잘 작동할 것이다.

*Now, let’s modify our code sample to a more realistic scenario. Let’s assume that the payment charge can fail due to insufficient balance and that we shouldn’t dispense any cash if that happens.*

이제 코드 샘플을 좀 더 현실적인 시나리오로 수정해 보자. 잔액이 부족하여 결제 수수료가 실패할 수 있으며, 그럴 경우 현금을 지급해서는 안 된다고 가정해 보자.

*This is where the nice separation of concerns I described above starts to break apart. The decision process now depends on information unavailable by the time we start that decision process. Here’s the code:*

여기서 위에서 설명한 우려의 멋진 분리가 깨지기 시작한다. 이제 의사 결정 프로세스는 해당 의사 결정 프로세스를 시작할 때까지 사용할 수 없는 정보에 따라 달라진다. 코드는 다음과 같습니다.

```java
public void WithdrawMoney(decimal amount)
{
    if (!_atm.CanDispenseMoney(amount))
        return ;
 
    decimal amountWithCommission = _atm.CalculateAmountWithCommission(amount);
    Result result = _paymentGateway.ChargePayment(amountWithCommission);
 
    if (result.IsFailure)
        return ;
 
    _atm.DispenseMoney(amount);
    _repository.Save(_atm);
}
```

*In this version, the second if statement we introduced does represent domain logic. It decides whether we will be dispensing cash to the user. However, unlike in the first conditional operator, it’s not the Atm entity who makes that decision. It’s the application service itself.*

이 버전에서 우리가 소개한 두 번째 if 문은 도메인 로직을 나타낸다. 그것은 우리가 사용자에게 현금을 지급할지 여부를 결정한다. 그러나, 첫 번째 조건부 운영자와 달리, 그러한 결정을 내리는 것은 ATM 주체가 아니다. 애플리케이션 서비스 자체이다.

*It is possible now to take cash from the Atm even if the payment has failed prior to it. The domain entity doesn’t hold this invariant for us. And it’s impossible to introduce such an invariant without violating the entity’s isolation because in order to check this precondition it will have to call the 3rd party service.*

그 전에 결제가 실패했더라도 이제는 ATM에서 현금을 인출할 수 있다. 도메인 엔티티는 우리에게 이 불변성을 유지하지 않는다. 그리고 기업의 격리를 위반하지 않고 그러한 불변량을 도입하는 것은 불가능하다. 왜냐하면 이 전제조건을 확인하기 위해서는 제3자 서비스를 호출해야 할 것이기 때문이다.

*So, what to do in such situation? This is where domain services can be helpful. You can attribute to them any business decisions which require additional information from the external world and which cannot be made by entities and value objects because of that*

그렇다면, 이런 상황에서 어떻게 해야 할까? 이것이 도메인 서비스가 도움이 될 수 있는 부분이다. 외부 세계의 추가 정보를 필요로 하고 ***entity*** 및 ***value objects***에 의해 이루어질 수 없는 모든 비즈니스 결정을 그들에게 귀속시킬 수 있다.

```java
public void WithdrawMoney(decimal amount)
{
    Atm atm = _repository.Get();
    _atmService.WithdrawMoney(atm, amount);
    _repository.Save(_atm);
}

public sealed class AtmService // Domain service
{
    public void WithdrawMoney(Atm atm, decimal amount)
    {
        if (!atm.CanDispenseMoney(amount))
            return ;
 
        decimal amountWithCommission = atm.CalculateAmountWithCommission(amount);
        Result result = _paymentGateway.ChargePayment(amountWithCommission);
 
        if (result.IsFailure)
            return;
 
        atm.DispenseMoney(amount);
    }
}
```

*The domain service here is a middle ground between impurity and the amount of complexity / business logic held. On one hand, we can’t make this service completely isolated because it has to work with the payment gateway in order to do its work. On the other, we don’t attribute too much domain logic to it, only the knowledge regarding how to exchange cash for credit.*

여기서 도메인 서비스는 불순물과 보유하고 있는 복잡성/비즈니스 논리 사이의 중간 지점이다. 한편으로는, 우리는 이 서비스를 완전히 고립시킬 수 없습니다. 왜냐하면 이 서비스가 일을 하기 위해서는 지불 게이트웨이와 함께 작동해야 하기 때문이다. 반면에, 우리는 너무 많은 도메인 논리를 그것에 돌리지 않고 단지 현금을 신용으로 교환하는 방법에 대한 지식만 돌린다.

*We still attribute as much logic as possible to the entity. For example, the act of dispensing cash is still a responsibility of Atm. And we still try to isolate the domain service as much as possible. For example, we don’t make it work with the repository as it is not required to make the business decision. The impurity and the domain logic introduced to the service are the bare minimum here. Just enough for it to work properly.*

우리는 여전히 가능한 한 많은 논리를 ***entity***에 귀속시킨다. 예를 들어 현금을 분배하는 행위는 여전히 ATM의 책임이다. 그리고 우리는 여전히 가능한 한 도메인 서비스를 격리하려고 노력한다. 예를 들어, 비즈니스 결정을 내릴 필요가 없기 때문에 저장소와 함께 작동하지 않는다. 서비스에 도입된 불순물과 도메인 로직은 여기서 최소한의 것이다. 제대로 작동하기에 충분하다.

This extraction may look questionable. Isn’t it just a shift of responsibilities with no practical benefits? There are some benefits, however. The code in the domain service is more testable than in the application service. It has fewer external dependencies and therefore we need to use fewer test doubles in order to unit test it. This service is of course not as testable as the entity but still. The second benefit is that this way we prevent domain knowledge leakage and keep all domain logic within the domain model boundary which may be helpful for readability.

이 추출은 의심스러운 것처럼 보일 수 있다. 실익이 없는 책임 전가 아닌가. 하지만 몇 가지 이점이 있다. 도메인 서비스의 코드는 애플리케이션 서비스보다 더 테스트 가능하다. 외부 의존성이 적기 때문에 단위 테스트를 위해 테스트 더블을 더 적게 사용해야 한다. 이 용역은 물론 기업만큼 시험할 수는 없지만 여전히 유효하다. 두 번째 이점은 이러한 방식으로 도메인 지식 유출을 방지하고 가독성에 도움이 될 수 있는 모든 도메인 논리를 도메인 모델 경계 내에 유지한다는 것이다.

## **Summary**

- *Domain services carry domain knowledge; application services don’t (ideally).*
- *Domain services hold domain logic that doesn’t naturally fit entities and value objects.*
- *Introduce domain services when you see that some logic cannot be attributed to an entity/value object because that would break their isolation.*

---

- 도메인 서비스는 도메인 지식을 전달하지만 애플리케이션 서비스는 그렇지 않다.
- 도메인 서비스는 ***entity*** 및 ***value object***에 자연스럽게 맞지 않는 도메인 로직을 보유한다.
- 일부 논리가 ***entity/value object***에 귀속될 수 없는 경우 해당 개체 분리가 중단되므로 도메인 서비스를 도입하자.

## Reference

**[Enterprise Craftsmanship: Domain services vs Application services](https://enterprisecraftsmanship.com/posts/domain-vs-application-services/)**