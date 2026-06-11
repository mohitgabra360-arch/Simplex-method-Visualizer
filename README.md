<!-- Simplex-method-Visualizer
#"A Python Tkinter-based Simplex Method Visualizer that helps students understand Linear Programming Problems through step-by-step tableau visualization. Displays each simplex iteration, pivot operations, entering/leaving variables, and the final optimal solution in an interactive GUI." -->
from tkinter import *
from tkinter import ttk
import matplotlib.pyplot as plt
import numpy as np
from matplotlib.backends.backend_tkagg import FigureCanvasTkAgg

def solve():
    try:
        A=float(eentry.get())
        B=float(e2entry.get())
        a1,b1,c1 = map(float,zentry.get().split(','))
        a2,b2,c2 = map(float,z2entry.get().split(','))
        <!-- CLEAR TABLE -->
        for row in table.get_children():
            table.delete(row)
        ax.clear()
        x = np.linspace(0, 50, 400)
        y1 = (c1 - a1 * x) / b1
        y2 = (c2 - a2 * x) / b2
        ax.plot(x, y1, label=f"{a1}x + {b1}y ≤ {c1}")
        ax.plot(x, y2, label=f"{a2}x + {b2}y ≤ {c2}")
        y = np.minimum(y1, y2)
        ax.fill_between(x, 0, y, where=(y >= 0), alpha=0.3, label="Feasible Region")
        points = []
        <!-- Origin -->
        points.append((0,0))
        <!-- Intercepts (Constraint 1) -->
        if a1 != 0:
            points.append((c1/a1,0))
        if b1 != 0:
            points.append((0,c1/b1))
         <!-- Intercepts (Constraint 2) -->
        if a2 != 0:
            points.append((c2/a2,0))
        if b2 != 0:
            points.append((0,c2/b2))
         <!-- Intercepts (Constraint 3) -->
        det = a1*b2 - a2*b1
        if det != 0:
            x_int = (c1*b2 - c2*b1)/det
            y_int = (a1*c2 - a2*c1)/det
            points.append((x_int,y_int))

        <!-- FILTER VALID FEASIBLE POINTS -->
feasible = []
        for x_val,y_val in points:
            if x_val >= 0 and y_val >= 0:
                if (a1*x_val + b1*y_val <= c1 + 1e-6) and (a2*x_val + b2*y_val <= c2 + 1e-6):
                    feasible.append((round(x_val,3), round(y_val,3)))

        <!-- REMOVE DUPLICATES CLEANLY -->
feasible = list(set(feasible))

        <!-- FIND OPTIMAL -->
  max_z = float('-inf')
        best_point = (0,0)
      for x_val,y_val in feasible:
            z = A*x_val + B*y_val
            table.insert("", "end", values=(f"({x_val},{y_val})", round(z,2)))
            if z > max_z:
                max_z = z
                best_point = (x_val,y_val)

        <!-- PLOT OPTIMAL POINT -->
  ax.scatter(best_point[0], best_point[1], color='red', label="Optimal Point")
      ax.set_xlim(0, 10)
        ax.set_ylim(0, 10)
        ax.set_xlabel("x")
        ax.set_ylabel("y")
        ax.set_title("Simplex Method Visualization")
        ax.legend()
        ax.grid()
        canvas.draw()
    except:
        pass


root = Tk()
root.state('zoomed')
root.grid_rowconfigure(0, weight=1)
root.grid_columnconfigure(0, weight=1)

root.title("Mini Project : Simplex Method Visualizer")

frame=LabelFrame(root)
frame.grid(row=0,column=0,sticky="nsew")

frame.grid_rowconfigure(0, weight=1)
frame.grid_columnconfigure(0, weight=1)
frame.grid_columnconfigure(1, weight=3)

lframe=LabelFrame(frame)
lframe.grid(row=0,column=0, sticky="nsew", padx=10, pady=10)

ilabel=Label(lframe,text="Enter values of objective function : ",font=("Arial",18))
ilabel.grid(row=0,column=0,padx=20,pady=20)

elabel=Label(lframe,text = "Maximize Z = Ax + By",font=("Arial",18))
elabel.grid(row=1,column=0)

alabel=Label(lframe,text = "A : ",font=("Arial",18))
alabel.grid(row=2,column=0)

eentry = Entry(lframe,font=("Arial",18))
eentry.grid(row=3,column=0)

blabel=Label(lframe,text = "B : ",font=("Arial",18))
blabel.grid(row=2,column=1)

e2entry = Entry(lframe,font=("Arial",18))
e2entry.grid(row=3,column=1)

clabel=Label(lframe,text="Enter Constraints ax + by <= c : ",font=("Arial",18))
clabel.grid(row=5,column=0,padx=5,pady=20)

zlabel = Label(lframe,text="First Constraint(a,b,c) : ",font=("Arial",18))
zlabel.grid(row=6,column=0,padx=5,pady=5)

zentry=Entry(lframe,font=("Arial",18))
zentry.grid(row=6,column=1,padx=5,pady=5)

z2label = Label(lframe,text="Second Constraint(a,b,c) : ",font=("Arial",18))
z2label.grid(row=7,column=0,padx=5,pady=5)

z2entry=Entry(lframe,font=("Arial",18))
z2entry.grid(row=7,column=1,padx=5,pady=5)

lframe.grid_columnconfigure(0, weight=1)
lframe.grid_columnconfigure(1, weight=1)

btn=Button(lframe,text="Solve",bg="green",fg="white",font=("Arial",18),command=solve)
btn.grid(row=8,column=0,columnspan=2,sticky="n",pady=30)

# STYLE
style = ttk.Style()
style.configure("Treeview.Heading", font=("Arial", 14, "bold"))
style.configure("Treeview", font=("Arial", 12), rowheight=30)

# TABLE
table = ttk.Treeview(lframe,columns=('first','second'),show="headings")
table.grid(row=9,column=0,columnspan=2)

table.heading('first',text='Corner Points (x,y)')
table.heading('second',text='Value of Z')

table.column('first', width=200, anchor='center')
table.column('second', width=120, anchor='center')

rframe=LabelFrame(frame)
rframe.grid(row=0,column=1, sticky="nsew", padx=10, pady=10)

glabel=Label(rframe,text="Graph Visualization",font=("Arial",25))
glabel.pack()

fig,ax = plt.subplots()
ax.plot()
ax.grid()
ax.set_title("Visualization")

canvas = FigureCanvasTkAgg(fig,rframe)
canvas.draw()
canvas.get_tk_widget().pack(fill="both", expand=True)

root.mainloop()
