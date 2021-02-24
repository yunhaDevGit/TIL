# Spring 프로젝트 만들기(gradle)

<img src="https://lh6.googleusercontent.com/7K2g0e1E6u2a9EU509Y0XqVqIuF4-eLXyD5x2V7lsVtFxKuG0NwfZuHSDqqp5CTa8L7hxiRDrHW0feVbJP4zqKBC8GIJAjwrkwvCoG4EXpsn53MVcqrhDIMxlb1n6h-36Lh1MLYa" alt="img" style="zoom:80%;" />



<img src="https://lh5.googleusercontent.com/mOsODkoPnUOZctgFM_zFN_fz1N0-nVHwEQeAugX6B_1QoXRKKjlkwaTbYyIMxmYfnnm_XQg_GqAYCXUO0MKl0bkIzeJacCMl8HHQgKcK5xymMifaNGguIA3aMwQnyXKu_tyWqTaW" alt="img" style="zoom:80%;" />



<img src="https://lh6.googleusercontent.com/O9DfdRI22HTT3N19qFCSF1Jvxt8_OrCwNsAYE5eU3Tini3wfB8c9xiboDajClYPwQOoTN_sLlyGAapKAMhNJy7GWQCIjL00dnls3ulCHlv_mFyAehRzf-DR0QnorvaQRGQOw0R8d" alt="img" style="zoom:80%;" />



![img](https://lh6.googleusercontent.com/6JoxzkAU_B3VxEaFOM2NW_JXjzewz_hL3wMm9YxuqEIv4Rj0FoqXdHa-kl-kR7NKclJZUgsI7dTCYUDPrvvY7QvTeBDz5fzBCmhFXpRBmjw5eyExPGyKRigfuw1wQQDS8o9diFGQ)

import gradle -> error :sweat:

![img](https://lh5.googleusercontent.com/LSt8f9TqSsiYGfAAUvYJgDIXC9-ZHxKEfJbtDm7dS3czSBKu5NbXIWIGPfxbO4VeVHAdAVmaVEru1iRqxTCNM3ibW2xvdROvhwqVRfQKn9M0hLrBzRo-RW8KQFyndkyFsn4RcEoE)

```log
Build file '/root/IdeaProjects/spring5fs/chap02/build.gradle' line: 26
A problem occurred evaluating root project 'chap02'.
> Cannot add task 'wrapper' as a task with that name already exists.
```

-> gradle version이 5.x이기 때문에 발생하는 에러. 다음과 같이 바꿔주면 된다

![img](https://lh4.googleusercontent.com/knI3W9_30Q3RgAyHZyV0yku8Gg-da1VqUeMLLMx_UttccHXxenEET8OayIaMMaTAFYTO8BLpegxFlxTtLFD75uNoIRjSHdOQ1G1DykbmjxomOlYU25sqxMvBvHxXarhmGUTNdq_1)