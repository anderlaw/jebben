---
title: "如何在Nodejs中使用JWT"
date: 2024-01-07T19:16:35+08:00
draft: false
featuredImage: banner.png
summary: JWT 是jsonwebtoken的简称，用于用户认证和权限鉴定。本文介绍一个名叫“jsonwebtoken”的npm包，它实现了JWT的常用的功能。
tags: ["JWT"]
---

`jsonwebtoken`是一个npm包，提供了关于jsonwebtoken的生成、检查方法。

## 安装

`npm install jsonwebtoken`

## 生成token

返回一个token字符串
```javascript
import jwt from "jsonwebtoken";
//语法：
export function sign(
    payload: string | Buffer | object,
    secretOrPrivateKey: Secret,
    options?: SignOptions,
): string;

jwt.sign(
    jsObject,
    privateKey,//密钥，可选。可为任意字符串
    { expiresIn: seconds，algorithm:"RS256" }
    //expiresIn 表示token的过期时间，数字表示秒数，字符串则有其他用法
);
//返回示例:
// eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjoxNzA0NDU1MTg1NzE2LCJ0eXBlIjoidGVzdCIsImlhdCI6MTcwNDQ1NTE4NSwiZXhwIjoxNzA0NDU4Nzg1fQ.EGWRaDvmNxkzaUT8FJ1Me-xEEWwkycT3WEzx6O09UBY
```

注意事项：在签写token时，`payload`尽量使用`object`类型的,否则的话设置过期时间时会抛出错误，*应该是个bug，没有理由string形式的payload不支持设置过期时间这一基本属性* 。
## 检验token

```javascript
export function verify(
    token: string,
    secretOrPublicKey: Secret,
    options?: VerifyOptions & { complete?: false },
): JwtPayload | string;
const decode_data = jwt.verify(token, privateKey);
//返回示例：
//{ iat,exp,...payload }
```
`verify`方法会检查token的有效性，有效则返回一个拷贝了原始对象属性、包含了`iat`，`exp`属性的新对象:
- iat表示token的生成时间戳（1970年来的秒
数）
- exp表示token的实效时间戳（1970年来的秒数）。


无效则会抛出错误,因此需要增加try、catch来捕获错误：
```javascript
try{
    jwt.verify(token,privateKey)
}catch(e){
    
}
```

## 其他设置

还可以设置`audience`属性来标记不同的token，在`options`对象传入字段：`audience`。
在`verify`时增加`audioence`属性来校验。
具体签写的options接口和验证的options接口如下：
```javascript
export interface SignOptions {
    /**
     * Signature algorithm. Could be one of these values :
     * - HS256:    HMAC using SHA-256 hash algorithm (default)
     * - HS384:    HMAC using SHA-384 hash algorithm
     * - HS512:    HMAC using SHA-512 hash algorithm
     * - RS256:    RSASSA using SHA-256 hash algorithm
     * - RS384:    RSASSA using SHA-384 hash algorithm
     * - RS512:    RSASSA using SHA-512 hash algorithm
     * - ES256:    ECDSA using P-256 curve and SHA-256 hash algorithm
     * - ES384:    ECDSA using P-384 curve and SHA-384 hash algorithm
     * - ES512:    ECDSA using P-521 curve and SHA-512 hash algorithm
     * - none:     No digital signature or MAC value included
     */
    algorithm?: Algorithm | undefined;
    keyid?: string | undefined;
    /** expressed in seconds or a string describing a time span [zeit/ms](https://github.com/zeit/ms.js).  Eg: 60, "2 days", "10h", "7d" */
    expiresIn?: string | number | undefined;
    /** expressed in seconds or a string describing a time span [zeit/ms](https://github.com/zeit/ms.js).  Eg: 60, "2 days", "10h", "7d" */
    notBefore?: string | number | undefined;
    audience?: string | string[] | undefined;
    subject?: string | undefined;
    issuer?: string | undefined;
    jwtid?: string | undefined;
    mutatePayload?: boolean | undefined;
    noTimestamp?: boolean | undefined;
    header?: JwtHeader | undefined;
    encoding?: string | undefined;
    allowInsecureKeySizes?: boolean | undefined;
    allowInvalidAsymmetricKeyTypes?: boolean | undefined;
}
```
```javascript
export interface VerifyOptions {
    algorithms?: Algorithm[] | undefined;
    audience?: string | RegExp | Array<string | RegExp> | undefined;
    clockTimestamp?: number | undefined;
    clockTolerance?: number | undefined;
    /** return an object with the decoded `{ payload, header, signature }` instead of only the usual content of the payload. */
    complete?: boolean | undefined;
    issuer?: string | string[] | undefined;
    ignoreExpiration?: boolean | undefined;
    ignoreNotBefore?: boolean | undefined;
    jwtid?: string | undefined;
    /**
     * If you want to check `nonce` claim, provide a string value here.
     * It is used on Open ID for the ID Tokens. ([Open ID implementation notes](https://openid.net/specs/openid-connect-core-1_0.html#NonceNotes))
     */
    nonce?: string | undefined;
    subject?: string | undefined;
    maxAge?: string | number | undefined;
    allowInvalidAsymmetricKeyTypes?: boolean | undefined;
}

```