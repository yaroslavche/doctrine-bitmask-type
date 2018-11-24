### Example

```php
# src\Entity\User.php

use BitMask\BitMaskInterface;
use Doctrine\ORM\Mapping as ORM;

/**
 * @ORM\Entity()
 */
class User
{
    const ROLE_USER = 1 << 0;
    const ROLE_EDITOR = 1 << 1;
    const ROLE_MODERATOR = 1 << 2;
    const ROLE_ADMIN = 1 << 3;
    const ROLE_SUPER_ADMIN = 1 << 4;

    // ...
    
    /**
     * bitmask roles
     * @ORM\Column(type="bitmask")
     */
    private $roles;

    public function getRoles(): BitMaskInterface
    {
        return $this->roles;
    }

    public function setRoles(BitMaskInterface $roles)
    {
        $this->roles = $roles;
    }

    public function hasRoleUser(): bool
    {
        return $this->getRoles()->isSetBit(static::ROLE_USER);
    }
    
    // ...
}

```

```php
# src/Controller/TestController.php

    // ...
    $em = $this->getDoctrine()->getManager();
    $user = new User();
    $rolesBitMask = new BitMask(User::ROLE_USER | User::ROLE_EDITOR);
    $user->setRoles($rolesBitMask);
    $em->persist($user);
    $em->flush();
    $repo = $this->getDoctrine()->getRepository(User::class);
    foreach ($repo->findAll() as $user) {
        dump(
            'is admin: ' . $user->isAdmin(),
            'is admin: ' . $user->getRoles()->isSetBit(User::ROLE_ADMIN)
        );
    }
    // ...
```
