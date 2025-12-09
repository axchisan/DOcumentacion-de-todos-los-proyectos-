# Entry Point and Routing

> **Relevant source files**
> * [index.php](https://github.com/GroveLive/abogado/blob/8bfc71d0/index.php)
> * [views/home.php](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php)

## Purpose and Scope

This document describes the application's entry point mechanism implemented in `index.php` and its redirect-based routing approach. The entry point handles initial HTTP requests to the application root and redirects them to the primary view.

This document focuses specifically on the redirect mechanism that serves as the application's entry point. For the complete request lifecycle including controller and model invocation, see [Request Lifecycle](/GroveLive/abogado/3.1-request-lifecycle). For details on the home view that receives the redirected requests, see [Lawyer Listing View (home.php)](/GroveLive/abogado/5.1.1-lawyer-listing-view-(home.php)).

---

## Overview

The Abogado application uses a minimal entry point design consisting of a single redirect from the root URL to the main view. There is no traditional routing system, URL pattern matching, or route configuration. Instead, the application employs direct file-based navigation where each view is accessed through its filesystem path.

**Key Characteristics:**

* Single entry point file: `index.php`
* HTTP 302 redirect to `views/home.php`
* No routing framework or dispatcher
* View-to-view navigation via direct URL parameters

**Sources:** [index.php L1-L5](https://github.com/GroveLive/abogado/blob/8bfc71d0/index.php#L1-L5)

---

## The Entry Point File

The `index.php` file serves as the application's entry point, located at the web root. It consists of three lines of PHP code that perform an immediate HTTP redirect:

```

```

### Implementation Details

| Aspect | Implementation |
| --- | --- |
| **File Location** | `/index.php` (web root) |
| **Total Lines** | 3 lines of PHP code |
| **HTTP Method** | 302 Found (temporary redirect) |
| **Target Location** | `views/home.php` (relative path) |
| **Control Flow** | Immediate exit after redirect header |

The `header()` function sets an HTTP Location header that instructs the browser to navigate to a new URL. The subsequent `exit` statement terminates PHP execution, ensuring no additional output is sent that could interfere with the redirect.

**Sources:** [index.php L1-L5](https://github.com/GroveLive/abogado/blob/8bfc71d0/index.php#L1-L5)

---

## Redirect Mechanism

### HTTP 302 Redirect Behavior

The redirect mechanism uses PHP's `header()` function to send an HTTP 302 (Found) status code. This indicates a temporary redirect, informing browsers and search engines that the resource has temporarily moved to a different location.

```

```

### URL Transformation

When a user navigates to the application root, the following URL transformation occurs:

| Step | URL | Description |
| --- | --- | --- |
| 1. Initial Request | `http://example.com/` | User accesses root URL |
| 2. Server Processes | `index.php` executes | PHP processes entry point |
| 3. Redirect Issued | `Location: views/home.php` | Relative path redirect header |
| 4. Browser Resolves | `http://example.com/views/home.php` | Browser constructs full URL |
| 5. Final Request | `GET /views/home.php` | Browser fetches actual content |

This results in two HTTP requests: the initial request to the root and the subsequent request to the home view.

**Sources:** [index.php L1-L5](https://github.com/GroveLive/abogado/blob/8bfc71d0/index.php#L1-L5)

---

## Routing Architecture Analysis

### Absence of Traditional Routing

The application does **not** implement a traditional routing system. Key routing features commonly found in web frameworks are absent:

| Traditional Router Feature | Status in This Application |
| --- | --- |
| URL pattern matching | Not implemented |
| Route definitions/configuration | Not present |
| Request dispatcher | Not present |
| Route parameters extraction | Not present |
| Named routes | Not present |
| Route middleware | Not present |
| RESTful routing | Not present |

### Direct File-Based Navigation

Instead of a routing system, the application uses direct file references for navigation:

```

```

In [views/home.php L22](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php#L22-L22)

 the navigation link to the profile view is constructed directly:

```

```

This direct file reference approach means:

* Views are responsible for constructing their own navigation URLs
* No central route registry exists
* URL structure directly mirrors filesystem structure
* Query parameters are manually appended to URLs

**Sources:** [index.php L1-L5](https://github.com/GroveLive/abogado/blob/8bfc71d0/index.php#L1-L5)

 [views/home.php L22](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php#L22-L22)

---

## Request Flow from Entry Point

The complete flow from initial request to view rendering involves these steps:

```

```

### Step-by-Step Breakdown

1. **Initial Request**: User navigates to the application root URL (e.g., `http://localhost/abogado/`)
2. **Entry Point Execution**: The web server routes the request to `index.php` [index.php L1-L5](https://github.com/GroveLive/abogado/blob/8bfc71d0/index.php#L1-L5)
3. **Redirect Header**: Line 2 executes `header("Location: views/home.php")`, setting the HTTP Location header
4. **Termination**: Line 3 executes `exit`, preventing any further PHP output
5. **Browser Response**: Browser receives HTTP 302 response with Location header
6. **Automatic Navigation**: Browser automatically issues a new GET request to `views/home.php`
7. **View Processing**: The home view loads, requiring the controller and rendering content [views/home.php L1-L5](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php#L1-L5)

**Sources:** [index.php L1-L5](https://github.com/GroveLive/abogado/blob/8bfc71d0/index.php#L1-L5)

 [views/home.php L1-L5](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php#L1-L5)

---

## Technical Implementation Details

### Header Function Behavior

The `header()` function must be called before any output is sent to the browser. In this implementation:

* **Timing**: Called at the very start of `index.php` before any HTML or whitespace
* **Function**: Sends raw HTTP headers to the browser
* **Type**: Location header indicating redirect
* **Status Code**: Default 302 (temporary redirect) - not explicitly specified

### Exit Statement Purpose

The `exit` statement [index.php L3](https://github.com/GroveLive/abogado/blob/8bfc71d0/index.php#L3-L3)

 serves critical purposes:

| Purpose | Description |
| --- | --- |
| **Prevent Additional Output** | Stops PHP from executing any code after the redirect header |
| **Resource Cleanup** | Allows PHP to perform cleanup operations |
| **Security** | Prevents accidental information disclosure from subsequent code |
| **Performance** | Avoids unnecessary processing after redirect decision |

Without `exit`, if any code followed the redirect header, it would execute unnecessarily (though the browser would still redirect).

**Sources:** [index.php L1-L5](https://github.com/GroveLive/abogado/blob/8bfc71d0/index.php#L1-L5)

---

## URL Structure and Navigation Pattern

### Application URL Map

The complete URL structure of the application:

```

```

### Why Redirect to views/home.php?

The redirect from root to `views/home.php` serves several purposes:

1. **URL Consistency**: Ensures all users access the home page through the same URL path
2. **Organization**: Keeps view files organized in the `views/` directory
3. **Entry Point Abstraction**: Allows the root URL to remain stable even if the home view location changes
4. **Clean Separation**: Separates the entry point logic from view rendering logic

However, this design has trade-offs:

| Advantage | Disadvantage |
| --- | --- |
| Clear entry point | Adds extra HTTP round-trip |
| Directory organization | No URL rewriting (exposes .php) |
| Simple to understand | No route abstraction |
| Easy to modify target | Visible file paths in URLs |

**Sources:** [index.php L1-L5](https://github.com/GroveLive/abogado/blob/8bfc71d0/index.php#L1-L5)

 [views/home.php L1-L27](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php#L1-L27)

---

## Comparison with Alternative Routing Approaches

### Current Implementation vs. Common Patterns

```

```

### Routing Pattern Comparison

| Pattern | Implementation | Used in Application? |
| --- | --- | --- |
| **Simple Redirect** | Single redirect to main page | ✓ Yes (current) |
| **Front Controller** | All requests through single entry, router dispatches | ✗ No |
| **URL Rewriting** | .htaccess rewrites URLs to entry point | ✗ No |
| **File-Based Routing** | Each file is its own endpoint | ✓ Partial (views) |
| **MVC Router** | Routes map to controller actions | ✗ No |

The current implementation uses a hybrid approach: a simple redirect entry point combined with direct file-based navigation for subsequent requests.

**Sources:** [index.php L1-L5](https://github.com/GroveLive/abogado/blob/8bfc71d0/index.php#L1-L5)

 [views/home.php L22](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php#L22-L22)

---

## Limitations and Considerations

### Current Limitations

1. **No URL Beautification**: URLs expose `.php` extensions and filesystem structure * Example: `/views/abogadosView.php?id=5` instead of `/abogados/5`
2. **No Route Guards**: No centralized mechanism to enforce authentication or authorization
3. **Limited Flexibility**: Adding new pages requires creating new physical files
4. **Duplicate Redirect Request**: Every root access incurs two HTTP requests
5. **Hard-Coded Paths**: View files contain hard-coded paths to other views [views/home.php L22](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php#L22-L22)

### Security Considerations

The simple redirect mechanism has minimal security implications:

* **No Input Processing**: `index.php` processes no user input
* **No Database Access**: No data operations in the entry point
* **Output Control**: The `exit` statement prevents unintended output
* **Relative Path**: Uses relative path, avoiding absolute URL redirect vulnerabilities

However, the lack of a routing layer means:

* No centralized request validation
* No CSRF protection at the routing level
* No rate limiting or request throttling

**Sources:** [index.php L1-L5](https://github.com/GroveLive/abogado/blob/8bfc71d0/index.php#L1-L5)

---

## Integration with Application Flow

### How Entry Point Connects to Other Components

After the redirect completes and `views/home.php` is loaded, the application follows the standard MVC pattern:

```

```

The entry point's responsibility is limited to:

1. Receiving the initial request
2. Issuing the redirect
3. Terminating execution

All subsequent application logic (controller instantiation, data fetching, rendering) occurs in the target view. For details on these subsequent steps, see:

* [Request Lifecycle](/GroveLive/abogado/3.1-request-lifecycle) for the complete flow
* [Controllers Layer](/GroveLive/abogado/4.2-controllers-layer) for controller behavior
* [Lawyer Listing View](/GroveLive/abogado/5.1.1-lawyer-listing-view-(home.php)) for home view implementation

**Sources:** [index.php L1-L5](https://github.com/GroveLive/abogado/blob/8bfc71d0/index.php#L1-L5)

 [views/home.php L1-L5](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php#L1-L5)

---

## Summary

The application's entry point and routing mechanism is intentionally minimal:

* **Entry Point**: `index.php` performs a single HTTP 302 redirect to `views/home.php`
* **No Router**: No URL pattern matching, route configuration, or dispatcher
* **Direct Navigation**: Views link directly to other views using filesystem paths
* **Simplicity**: Easy to understand but lacks flexibility of modern routing systems
* **Two-Request Pattern**: Root access requires two HTTP requests (redirect + content)

This approach prioritizes simplicity and code clarity over features like URL rewriting, route abstraction, and centralized request handling. The design is suitable for small applications with a limited number of pages and straightforward navigation patterns.

**Sources:** [index.php L1-L5](https://github.com/GroveLive/abogado/blob/8bfc71d0/index.php#L1-L5)

 [views/home.php L1-L27](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php#L1-L27)