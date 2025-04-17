# C and Rust : A Tale of Two Languages

One of the defining traits of the C programming language is that it trusts you. Entirely. That trust comes with power — and also with the burden of micromanagement.

Rust, by contrast, is not so much about trust as it is about **partnership**. It assumes you're intelligent — but also acknowledges that you might be tired, distracted, or moving too fast.

Let’s take a deceptively simple task:  

> **Compute the square root of only the non-negative numbers in a list, skipping any missing (null) entries, and find the average.**

It’s the kind of problem that surfaces everything: memory safety, null handling, arithmetic correctness, and transformation clarity.

---

## 🛠️ In C: The Power and the Pain

```c
#include <stdio.h>
#include <math.h>
#include <stdlib.h>

int main() {
    double v1 = 1.0, v2 = 2.5, v3 = 4.0, v4 = -3.0, v5 = 3.5;
    double* data[] = { &v1, NULL, &v2, &v3, &v4, NULL, &v5 };
    size_t len = sizeof(data) / sizeof(data[0]);

    double sum = 0.0;
    int count = 0;

    for (size_t i = 0; i < len; ++i) {
        if (data[i] == NULL) continue;
        double x = *data[i];
        if (x < 0.0) continue;

        double root = sqrt(x);
        if (isnan(root)) continue;

        sum += root;
        count++;
    }

    if (count > 0) {
        printf("Average: %f\n", sum / count);
    } else {
        printf("Average: None\n");
    }

    return 0;
}
```

---

## 🦀 In Rust: The Compiler Has Your Back

Try this Rust version in the [Rust Playground](https://play.rust-lang.org/)!

```rust
fn main() {
    let data = vec![
        Some(1.0), None, Some(2.5), Some(4.0), Some(-3.0), None, Some(3.5),
    ];

    let average = data
        .into_iter()
        .flatten()
        .filter(|&x| x >= 0.0)
        .map(|x: f64| x.sqrt())
        .fold((0.0, 0), |(sum, count), x| (sum + x, count + 1))
        .pipe(|(sum, count)| if count > 0 { Some(sum / count as f64) } else { None });

    println!("Average: {:?}", average);
}

trait Pipe: Sized {
    fn pipe<F, R>(self, f: F) -> R where F: FnOnce(Self) -> R { f(self) }
}
impl<T> Pipe for T {}
```

---

## ⚖️ C vs Rust at a Glance

| Feature                  | C                        | Rust                          |
| ------------------------ | ------------------------ | ----------------------------- |
| Null Handling            | Manual (`NULL`)          | `Option<T>` with `.flatten()` |
| Negative Value Filtering | `if (x < 0)`             | `.filter(|x| x >= 0.0)`       |
| Square Root              | `sqrt(x)`                | `.map(|x: f64| x.sqrt())`     |
| Average Calculation      | Manual loop, count check | `.fold()` + `.pipe()`         |
| Safety                   | On you                   | Built into compiler           |
| Error Reporting          | None                     | Rich and actionable           |

---

## 🧠 Why Functional Style Wins in Rust

The **functional approach** in Rust isn't just prettier. It’s more reliable.

You think in terms of **data transformation pipelines**, which the compiler understands. That means better error messages, fewer bugs, and much easier reasoning. You spend more time solving the real problem — not fighting undefined behavior.

In C, code that looks simple often hides a web of assumptions and edge cases. In Rust, the same logic is **self-documenting, verifiable, and testable**.

---

## ✨ Conclusion

C is fast, direct, and timeless. But it demands constant vigilance.  
Rust is just as fast — but insists on correctness from the start.

If you’re building systems that need **both performance and safety**, Rust isn't just an option. It’s a statement: _we care about correctness_.

---

## Notes

For comparison, here is an imperative style equivalent of the Rust program.

```rust
fn main() {
    let v1 = 1.0;
    let v2 = 2.5;
    let v3 = 4.0;
    let v4 = -3.0;
    let v5 = 3.5;

    let data: Vec<Option<f64>> = vec![Some(v1), None, Some(v2), Some(v3), Some(v4), None, Some(v5)];

    let mut sum = 0.0;
    let mut count = 0;

    for item in &data {
        if let Some(x) = item {
            if *x >= 0.0 {
                let root = x.sqrt();
                if !root.is_nan() {
                    sum += root;
                    count += 1;
                }
            }
        }
    }

    if count > 0 {
        println!("Average: {}", sum / count as f64);
    } else {
        println!("Average: None");
    }
}

```

