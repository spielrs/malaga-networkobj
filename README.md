# Malaga HTTP Utils

`malaga_http_utils` is a small library to handler request and response objects

## Documentation

[https://docs.rs/malaga_http_utils/0.1.4/malaga_http_utils/](https://docs.rs/malaga_http_utils/0.1.4/malaga_http_utils/)

## Example:

```rust
#[macro_use]
extern crate serde_derive;

extern crate serde_json;
extern crate serde;
extern crate bincode;
extern crate malaga_http_utils;

use malaga_http_utils::{HttpObj, utils::*};
use std::str;

impl HttpObj for TestReq {
   fn serialize_to_struct(req: &[u8]) -> Self {
       let req_string = str::from_utf8(req).unwrap();
       let ser_req: TestReq = serde_json::from_str(req_string).unwrap();
       
       ser_req
   }

    fn serialize_to_binary(req: TestReq) -> Vec<u8> {
        let ser_req: Vec<u8> = bincode::serialize(&req).unwrap();

        ser_req
    } 
}

#[derive(Serialize, Deserialize, Debug, PartialEq)]
    struct TestHeader {
        #[serde(rename = "Content-Type")]
        content_type: String,
        #[serde(rename = "Authorization")]
        pub authorization: String,
    }

#[derive(Serialize, Deserialize, Debug, PartialEq)]
struct Body {
    user: String,
}

#[derive(Serialize, Deserialize, Debug, PartialEq)]
struct TestReq {
    headers: TestHeader,
    method: Methods,
    body: Body,
}

fn main() {
    let resq = b"{
       \"headers\": {
           \"Content-Type\": \"application/json\",
           \"Authorization\": \"Basis 1ddmcdd\"
       },
       \"method\": \"POST\",
       \"body\": {
           \"user\":\"test\"
       }
   }";

   let resq_struct_ser: TestReq = TestReq::serialize_to_struct(resq);
   let resq_binary_ser: Vec<u8> = TestReq::serialize_to_binary(resq_struct_ser);
   let req_string: TestReq = bincode::deserialize(&resq_binary_ser[..]).unwrap();

   assert_eq!(req_string, TestReq{
       headers: TestHeader {
           content_type: String::from("application/json"),
           authorization: String::from("Basis 1ddmcdd"),
       },
       method: Methods::POST,
       body: Body {
           user: String::from("test")
       }
   });
}
```

## License

Malaga HTTP Utils is MIT licensed. See [license](LICENSE)