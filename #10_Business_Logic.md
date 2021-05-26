### Business Logic Vulnerabilities

- **Excessive trust in client-side controls**

  in this case developer assume that user will interact with app via web interface only hence he validates inputs in client side only .

  Contexts example :

  - baskets in online markets
  - 2FA "two factor authentication"

- **Failing to handle unconventional input**

  Contexts example :

  - negative input on shop card 
  - null attack to break items limit
  - truncate input to fit variable "over load input"

- **Making flawed assumptions about user behavior**

  Contexts example :

  - update normal email to corporate domain with out validation

- **Users won't always supply mandatory input**

  Contexts example :

  - delete current password field when reset password

- **Users won't always follow the intended sequence**

  * `/cart/order-confirmation?order-confirmation=true` if we add items after this transaction and resend it may be accepted
  * drop user-role request and developer set it admin by default  

- **Domain-specific flaws**

  - use coupons more than one time by alter between two coupons
  - buy an infinite gift cards repeat process using burp macros

  