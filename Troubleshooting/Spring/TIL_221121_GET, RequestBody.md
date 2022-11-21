# @GetMapping @RequestBody

@GetMapping을 하면서 파라미터를 @RequestBody로 받도록 구현을 했다. 

Postman에서는 해당 요청이 잘 동작해서 그냥 넘어갔는데, 프론트 코드와 연동을 하고 요청을 확인해보니 계속해서 RequestBody가 null로 들어왔다. 

@RequestBody는 POST, PUT과 같이 body가 있는 요청에서만 사용할 수 있다. 

GET의 경우 @RequestParam으로 파라미터를 받아야 한다!
