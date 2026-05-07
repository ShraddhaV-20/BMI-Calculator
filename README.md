# BMI-Calculator
A beginner-friendly Python project takes a user's weight and height as input, computes their Body Mass Index (BMI), and classifies it into one of four health categories — Underweight, Normal, Overweight, or Obese. It also provides three actionable health tips tailored to the result, along with visualizations.
import matplotlib.pyplot as plt
import matplotlib.patches as mpatches
import numpy as np

def get_user_input():
    print("=" * 40)
    print("        BMI CALCULATOR")
    print("=" * 40)
    name   = input("Enter your name: ")
    age    = int(input("Enter your age: "))
    weight = float(input("Enter your weight (kg): "))
    height = float(input("Enter your height (cm): "))
    return name, age, weight, height


def calculate_bmi(weight, height):
    height_m = height / 100
    bmi = weight / (height_m ** 2)
    return round(bmi, 2)

def get_category(bmi):
    if bmi < 18.5:
        return "Underweight"
    elif bmi < 24.9:
        return "Normal weight"
    elif bmi < 29.9:
        return "Overweight"
    else:
        return "Obese"

def get_health_tips(category):
    tips = {
        "Underweight": [
            "Eat calorie-rich whole foods like nuts, dairy, and bananas.",
            "Add strength training to build muscle mass.",
            "Eat smaller meals more frequently throughout the day."
        ],
        "Normal weight": [
            "Great job! Maintain your weight with a balanced diet.",
            "Stay active with at least 30 mins of exercise daily.",
            "Keep up with regular health checkups."
        ],
        "Overweight": [
            "Focus on portion control and reduce sugary foods.",
            "Aim for 45–60 mins of moderate exercise daily.",
            "Drink more water and reduce processed food intake."
        ],
        "Obese": [
            "Consult a doctor or dietitian for a personalised plan.",
            "Start with low-impact exercises like walking or swimming.",
            "Track your meals to stay aware of your calorie intake."
        ]
    }
    return tips[category]

def display_result(name, age, bmi, category):
    print("\n" + "=" * 40)
    print(f"  Results for {name} (Age: {age})")
    print("=" * 40)
    print(f"  BMI Score  : {bmi}")
    print(f"  Category   : {category}")
    print("\n  💡 Health Tips:")
    for i, tip in enumerate(get_health_tips(category), 1):
        print(f"  {i}. {tip}")
    print("=" * 40)

def plot_gauge(bmi, category, name):
    fig, ax = plt.subplots(figsize=(8, 4), subplot_kw={"projection": "polar"})
    fig.patch.set_facecolor("#f9f9f9")

    # BMI scale capped at 40 for display
    bmi_min, bmi_max = 10, 40

    # Color bands: Underweight / Normal / Overweight / Obese
    bands = [
        (10, 18.5, "#5dade2", "Underweight"),
        (18.5, 24.9, "#2ecc71", "Normal"),
        (24.9, 29.9, "#f39c12", "Overweight"),
        (29.9, 40,   "#e74c3c", "Obese"),
    ]

    def bmi_to_angle(val):
        # Map BMI range to 180° arc (π to 0, left to right)
        clamped = max(bmi_min, min(bmi_max, val))
        return np.pi - (clamped - bmi_min) / (bmi_max - bmi_min) * np.pi

    # Draw each color band
    for start, end, color, _ in bands:
        theta_start = bmi_to_angle(end)
        theta_end   = bmi_to_angle(start)
        theta = np.linspace(theta_start, theta_end, 100)
        ax.fill_between(theta, 0.6, 1.0, color=color, alpha=0.85)

    # Needle
    needle_angle = bmi_to_angle(bmi)
    ax.annotate("", xy=(needle_angle, 0.95),
                xytext=(needle_angle, 0.0),
                arrowprops=dict(arrowstyle="-|>", color="black", lw=2))

    # BMI label at centre
    ax.text(0, 0, f"{bmi}", ha="center", va="center",
            fontsize=22, fontweight="bold", color="black")

    # Category label
    cat_colors = {
        "Underweight": "#5dade2", "Normal weight": "#2ecc71",
        "Overweight": "#f39c12",  "Obese": "#e74c3c"
    }
    ax.text(0, -0.3, category, ha="center", va="center",
            fontsize=13, fontweight="bold",
            color=cat_colors.get(category, "black"))

    # Legend patches
    legend_patches = [
        mpatches.Patch(color="#5dade2", label="Underweight (<18.5)"),
        mpatches.Patch(color="#2ecc71", label="Normal (18.5–24.9)"),
        mpatches.Patch(color="#f39c12", label="Overweight (25–29.9)"),
        mpatches.Patch(color="#e74c3c", label="Obese (≥30)"),
    ]
    ax.legend(handles=legend_patches, loc="lower center",
              bbox_to_anchor=(0.5, -0.15), ncol=2, fontsize=9)

    # Clean up axes
    ax.set_theta_zero_location("W")
    ax.set_theta_direction(-1)
    ax.set_ylim(0, 1)
    ax.axis("off")

    ax.set_title(f"BMI Gauge — {name}", fontsize=14,
                 fontweight="bold", pad=20)
    plt.tight_layout()
    plt.savefig("bmi_gauge.png", dpi=150, bbox_inches="tight")
    plt.show()
    print("  📊 Gauge chart saved as bmi_gauge.png")

def plot_bar_chart(bmi, category, name):
    categories   = ["Underweight", "Normal weight", "Overweight", "Obese"]
    ranges       = [18.5, 24.9, 29.9, 40]       # upper bound of each band
    colors       = ["#5dade2", "#2ecc71", "#f39c12", "#e74c3c"]
    bar_heights  = [18.5, 6.4, 5.0, 10.1]        # width of each band

    fig, ax = plt.subplots(figsize=(8, 5))
    fig.patch.set_facecolor("#f9f9f9")
    ax.set_facecolor("#f9f9f9")

    bars = ax.bar(categories, bar_heights, color=colors, alpha=0.75,
                  edgecolor="white", linewidth=1.5, width=0.5)

    # Highlight the user's category
    for i, cat in enumerate(categories):
        if cat == category:
            bars[i].set_alpha(1.0)
            bars[i].set_edgecolor("black")
            bars[i].set_linewidth(2.5)
            ax.text(i, bar_heights[i] + 0.3, f"← You\nBMI {bmi}",
                    ha="center", fontsize=10, fontweight="bold", color="black")

    # BMI range labels on bars
    range_labels = ["< 18.5", "18.5 – 24.9", "25 – 29.9", "≥ 30"]
    for i, (bar, label) in enumerate(zip(bars, range_labels)):
        ax.text(bar.get_x() + bar.get_width() / 2,
                bar.get_height() / 2,
                label, ha="center", va="center",
                fontsize=9, color="white", fontweight="bold")

    ax.set_title(f"BMI Category Chart — {name}",
                 fontsize=14, fontweight="bold", pad=15)
    ax.set_ylabel("BMI Band Width", fontsize=11)
    ax.set_xlabel("Category", fontsize=11)
    ax.set_ylim(0, max(bar_heights) + 3)
    ax.spines[["top", "right"]].set_visible(False)

    plt.tight_layout()
    plt.savefig("bmi_bar_chart.png", dpi=150, bbox_inches="tight")
    plt.show()
    print("  📊 Bar chart saved as bmi_bar_chart.png")

def main():
    name, age, weight, height = get_user_input()
    bmi      = calculate_bmi(weight, height)
    category = get_category(bmi)

    display_result(name, age, bmi, category)

    print("\n  Generating charts...")
    plot_gauge(bmi, category, name)
    plot_bar_chart(bmi, category, name)

if __name__ == "__main__":
    main()
