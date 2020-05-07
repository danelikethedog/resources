# Doxygen Guidelines

The intent of this document is to guide consistent style and content in the documentation of code. This will include things such as proper tags to use, indentation, necessary components to document, etc.

## Basic Style Guide
- We will be using the java style of documentation. That means tags begin with an `@` instead of a `\`.
- For clarity and consistency, we should deem doxygen content and style important as though it were physical C code.

## Tags to Use

Here is a basic example of a documented API with proper spacing.

```c
/**
 * @brief   Get an interface handle by an existent interface handle.
 *
 * This API will return an interface handle based on a provided interface handle. If the provided
 * interface handle was disabled, this API will return #AZ_ULIB_DISABLED_ERROR.
 *
 * Get a interface handle will increment the number of references to this interface, which means
 * that the IPC will know how many components is using this interface. When a component does not
 * need this interface anymore, it shall release it by calling az_ulib_ipc_release_interface().
 *
 * @note    **Do not release an interface will cause memory leak.**
 *
 * @param[in]   original_interface_handle The #az_ulib_ipc_interface_handle with the original
 *                                        interface handle. It cannot be `NULL`.
 * @param[out]  interface_handle          The #az_ulib_ipc_interface_handle* with the memory to
 *                                        store the interface handle. It cannot be `NULL`.
 *
 * @return The #az_ulib_result with the result of the get handle.
 *    @retval #AZ_ULIB_SUCCESS                  If the interface was found and the returned handle
 *                                              can be used.
 *    @retval #AZ_ULIB_ILLEGAL_ARGUMENT_ERROR   If one of the arguments is invalid.
 *    @retval #AZ_ULIB_DISABLED_ERROR           If the required interface was disabled and cannot be
 *                                              used anymore.
 */
static inline az_ulib_result az_ulib_ipc_get_interface(
    az_ulib_ipc_interface_handle original_interface_handle,
    az_ulib_ipc_interface_handle* interface_handle);
```
### High Level Components
- Brief description of code
- Detailed description of code (functionality, special notes, warnings, etc)
- Parameters
- Return value type
- Return values

### Low Level Breakdown
1. At the top should be an opening comment tag with two asterisks aka `/**`. This designates the comment as a doxygen comment and the doxygen parser will process it.
2. On its own line should be the opening `@brief` tag which specifies _briefly_ what the API or struct does. It should be about one sentence.
3. Put a new line between the `@brief` tag and any additional information you have for the API. Further description of the API can go after the `@brief` and before description of the parameters.
4. The list of parameters should have a new line between the first param and the previous description. They start with a `@param` tag and are designated as `[in]`, `[out]`, or `[in/out]` parameters. They should be marked accordingly. There should be a tab between the the `in/out` tag and the name of the parameter. The description of the parameter should be tab aligned next to the parameter name.
5. Finally the return type and values should be documented. The first tag `@return` should be used to document the return type of the function. Below that, indented by a tab should be a list of `@retval` values which detail what the possible return values of the function are and, helpfully, what the conditions are to return such a value.

## Header File Prefix
All header files that are to have documentation generated should have at the top a comment resembling the following:
```c
/**
 *  @file <insert file name>
 *  
 *  @brief <Insert brief description of file>
 *  
 *  (Optional additional information about the entire file)
 */
```
[Example here](https://github.com/Azure/azure-ulib-c/blob/975efa4e287dc26db6b081ddc53bb45db0dae29c/inc/az_ulib_ipc_api.h#L26)

## Useful Tags
- `@brief` : to open documentation comments
- `@param` : to document parameters to functions
- `@note` : if you would like to add a note which stands out from normal text
- `@warning` : if you would like to add a warning which stands out from normal text
- `@code` and `@endcode` : to have code blocks similar to the triple backtick of markdown. Example generated doc with code block [here](https://azure.github.io/azure-ulib-c/ustream__base_8h.html#ab8c2c90c6d5a065610cdf54c5f48aab4).
- `#` : use to signal to doxygen to add a link to a struct, type, or function. These should be used on all SDK created types and enum values. Functions can be directly referenced with the function name followed by parenthesis. Example being `Call the az_iot_init() function and everything will work`. If `az_iot_init` is a documented function, doxygen will link to it in the generated docs.
  - Notice that all the enums in above documentation are preceded with a `#`. This is necessary to link to the enum types in the code.
- Backticks, like in markdown, can be used to add a style to text resembling code. Use this when referencing variable names or other code snippets or things like `NULL`.
- Bolded text can be used similar to markdown using surrounding asterisks a la `**<text>**`

## Useful Links
- [Doxygen tag documentation](http://www.doxygen.nl/manual/commands.html)
- [uLib Repo inc directory](https://github.com/Azure/azure-ulib-c/tree/master/inc). In my humble opinion the code is pretty well documented.
- [uLib Repo generated docs](https://azure.github.io/azure-ulib-c/index.html)