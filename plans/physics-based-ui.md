# Tacit ideas / Plans / Physics-based UI

Who says that it makes sense for UIs to be hierarchical? Do related visual elements fit a strict hierarchy? Does that hierarchy apply to both UI input data and UI output events?

Why not apply something inspired by video games?

* UI is a scene
  * Some elements are hierarchical, some not
  * Some elements visible, some culled
* Elements backed by data lists
  * Don't focus on updating a complex state machine piece by piece
  * Focus on updating everything every frame
* Kinetics based on physics
  * Not durations or timing functions
  * But real Newtonian physics based on mass and force
* Graphics based on physics
  * Not pixel-width shadows or Gaussian blurs
  * But space, light, conservation of energy, rendering equations, and physical materials
